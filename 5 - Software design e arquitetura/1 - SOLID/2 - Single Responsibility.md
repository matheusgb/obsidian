## Definindo Single Responsibility

Em algumas literaturas, vamos encontrar a definição de [[1 - O que é SOLID#^92fdc5|Single Responsibility]] como: “**uma classe só pode ter uma única função”**.

Embora seja totalmente válida essa definição, infelizmente ela pode levar à alguns equívocos, como por exemplo, implementar uma classe para cada operação com o banco de dados.

Equívoco totalmente aceitável. Afinal, uma classe que salva e busca dados em um banco de dados, não estaria tendo duas funções?!

Uncle Bob define Single Responsibility Principle um pouco diferente. Ele diz que, “**cada módulo de um software deve ter um e somente um motivo para ser alterado**”.

Essa definição nos dá uma visão um pouco mais ampla. No meu entender, ela comporta melhor a ideia de ter uma classe para trabalhar todo tipo de transação com banco de dados. Afinal, essa classe ou módulo só precisará de mudanças quando algo relacionado com banco de dados precisar ser alterado.

## Sem Single Responsibility

Para ficar mais clara a implementação das definições dadas acima, vamos iniciar com um código que não respeita Single Responsibility.

Nesse código, vamos criar duas structs. Uma como modelo do banco de dados, e outra como um serviço para envio de e-mail. Também vamos implementar uma função para iniciar o serviço. Dessa forma, garantimos que todos os parâmetros necessários para que ele funcione corretamente sejam passados.

Por fim, adicionaremos um método `Send` à nossa struct de serviço. Esse método irá salvar os dados no banco de dados _**E**_ enviar um e-mail.

```go
type EmailGorm struct {
    gorm.Model
    From    string
    To      string
    Subject string
    Message string
}

type EmailService struct {
    db           *gorm.DB
    smtpHost     string
    smtpPassword string
    smtpPort     int
}

func NewEmailService(db *gorm.DB, smtpHost string, smtpPassword string, smtpPort int) *EmailService {
    return &EmailService{
        db:           db,
        smtpHost:     smtpHost,
        smtpPassword: smtpPassword,
        smtpPort:     smtpPort,
    }
}

func (s *EmailService) Send(from string, to string, subject string, message string) error {
    email := EmailGorm{
        From:    from,
        To:      to,
        Subject: subject,
        Message: message,
    }

    err := s.db.Create(&email).Error
    if err != nil {
        log.Println(err)
        return err
    }
    
    auth := smtp.PlainAuth("", from, s.smtpPassword, s.smtpHost)
    
    server := fmt.Sprintf("%s:%d", s.smtpHost, s.smtpPort)
    
    err = smtp.SendMail(server, auth, from, []string{to}, []byte(message))
    if err != nil {
        log.Println(err)
        return err
    }

    return nil
}
```

Se repararmos bem na última frase antes do exemplo de código, verá que eu dei uma certa ênfase ao “_**E”**_. Fiz pois ele mostra que estamos desrespeitando o princípio de Single Responsibility.

Esse _**“E”**_, deixa bem claro que o método está fazendo mais de uma coisa, ou seja, tem mais de uma responsabilidade.

Além disso, ele também viola a definição do Uncle Bob, já que caso seja necessário qualquer alteração em relação ao modelo do banco de dados ou, ao processo de envio do e-mail, o mesmo método precisa ser alterado.

## Com Single Responsibility

Para não violar o princípio de Single Responsibility, precisamos separar nosso código em outros blocos, dividindo assim as responsabilidades.

Ao analisar o “_**E**_” do exemplo anterior, é possível ver que, para dividir bem as responsabilidades, vamos precisar de uma struct para lidar com banco de dados e outra para lidar com envio do e-mail.

```go
type EmailGorm struct {
    gorm.Model
    From    string
    To      string
    Subject string
    Message string
}

// BANCO DE DADOS
type EmailRepository interface {
    Save(from string, to string, subject string, message string) error
}

type EmailDBRepository struct {
    db *gorm.DB
}

func NewEmailRepository(db *gorm.DB) EmailRepository {
    return &EmailDBRepository{
        db: db,
    }
}

func (r *EmailDBRepository) Save(from string, to string, subject string, message string) error {
    email := EmailGorm{
        From:    from,
        To:      to,
        Subject: subject,
        Message: message,
    }

    err := r.db.Create(&email).Error
    if err != nil {
        log.Println(err)
        return err
    }

    return nil
}

// ENVIO DE E-MAIL
type EmailSender interface {
    Send(from string, to string, subject string, message string) error
}

type EmailSMTPSender struct {
    smtpHost     string
    smtpPassword string
    smtpPort     int
}

func NewEmailSender(smtpHost string, smtpPassword string, smtpPort int) EmailSender {
    return &EmailSMTPSender{
        smtpHost:     smtpHost,
        smtpPassword: smtpPassword,
        smtpPort:     smtpPort,
    }
}

func (s *EmailSMTPSender) Send(from string, to string, subject string, message string) error {
    auth := smtp.PlainAuth("", from, s.smtpPassword, s.smtpHost)

    server := fmt.Sprintf("%s:%d", s.smtpHost, s.smtpPort)

    err := smtp.SendMail(server, auth, from, []string{to}, []byte(message))
    if err != nil {
        log.Println(err)
        return err
    }

    return nil
}

// SERVIÇO
type EmailService struct {
    repository EmailRepository
    sender     EmailSender
}

func NewEmailService(repository EmailRepository, sender EmailSender) *EmailService {
    return &EmailService{
        repository: repository,
        sender:     sender,
    }
}

func (s *EmailService) Send(from string, to string, subject string, message string) error {
    err := s.repository.Save(from, to, subject, message)
    if err != nil {
        return err
    }

    return s.sender.Send(from, to, subject, message)
}
```

Além das novas structs, notem que foram criadas as interfaces `EmailRepository` e `EmailSender`. Essas interfaces ajudam manter um service agnóstico a implementação do como e por quem as tarefas serão realizadas.

Em outras palavras, caso no futuro decidirmos que o envio será feito pelo MailGun, desde que a implementação siga a interface `EmailSender`, nada precisará ser alterado na implementação do service.

**Mas o método `Send` do `EmailService` não continua com duas responsabilidades?**

Na verdade não! Ele só delega a responsabilidade na execução das tarefas para as outras structs. No final, ele continua tendo somente uma responsabilidade, delegar as execuções para outras structs.

Identificar métodos com a responsabilidade de delegar tarefas é simples. Faça a seguinte pergunta para si mesmo: “Se eu remover esse método, continuo tendo a capacidade de executar as tarefas nele presentes?”

Se a resposta for **sim**, então você tem um método cuja a responsabilidade é somente delegar. Se a resposta for **não** e, ele estiver executando mais de uma tarefa, então provavelmente você está violando o princípio de Single Responsibility.