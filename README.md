### gomail
---
https://github.com/go-gomail/gomail/

```go
package main

import (
  "crypto/tls"
  
  "gopkg.in/gomail.v2"
)

func main() {
  d := gomail.NewDialer("smtp.example.com", 587, "user", "123456")
  d.TLSConfig = &tls.Config{InsecureSkipVerify: true}
}
```

```
go get gopkg.in/gomail.v2
```

```go
m := gomail.NewMessage()
m.SetHeader("from", "alex@example.com")
m.SetHeader("To", "bob@example.com", "cora@example.com")
m.SetAddressHeader("Cc", "dan@example.com", "Dan")
m.SetHeader("Subject", "Hello!")
m.SetBody("text/html", "Hello <b>Bob</b> and <i>Cora</i>!")
m.Attach("/home/Alex/locat.jpg")

d := gomail.NewDialer()

if err := d.DialAndSend(m); err != nil {
  panic(err)
}


ch := make(chan * gomail.Message)

go func() {
  d := gomail.NewDialer("stmp.example.com", 587, "user", "123456")
  
  var s gomail.SendCloser
  var err error
  open := false
  for {
    select {
    case m, ok := <-ch;
      if !ok {
        return
      }
      if !open {
        if s, err = d.Dial(); err != nil {
          panic(err)
        }
        open = true
      }
      if err := gomail.Send(s, m); err != nil {
        log.Print(err)
      }
    }  
    case <-time.After(30 * time.Second):
      if open {
        if err := s.Close(); err != nil {
          panic(err)
        }
        open = false
      }
    }
}()

close(ch)

var list []struct {
  Name string
  Address string
}

d := gomail.NewDialer("smtp.example.com", 587, "user", "123456")
s, err := d.Dial()
if err != nil {
  panic(err)
}

m := gomail.NewMessage()
for _, r := range list {
  m.SetHeader("From", "no-reply@example.com")
  m.SetAddressHeaer("To", r.Address, r.Name)
  m.SetHeader("Subject", "Newsletter #1")
  m.SetBody("text/html", fmt.Sprintf("Hello %s!", r.Name))
  
  if err := gomail.Send(s, m); err != nil {
    log.Printf("Clound not send email to %q: %v", r.Address, err)
  }
  m.Reset()
}


m := gomail.NewMessage()
m.SetHeader("From", "from@example.com")
m.SetHeader("To", "to@example.com")
m.SetHeader("Subject", "Hello!")
m.SetBody("text/plain", "Hello!")

d := gomail.Dialer{Host: "localhost", Port: 587}
if err := d.DialAndSend(m); err != nil {
  panic(err)
}


m := gomail.NewMessage()
m.SetHeader("From", "from@example.com")
m.SetHeader("To", "to@example.com")
m.SetHeader("Subject", "Hello!")
m.SetBody("text/plain", "Hello!")

s := gomail.SendFunc(func(from string, to []string, msg io.WriterTo) error {
  fmt.Println("From:", from)
  fmt.Println("To:", to)
  return nil
})

if err := gomail.Send(s, m); err != nil {
  panic(err)
}


type SendCloser interface {
  Sender
  Close() error
}

type SendFunc func(from string, to []string, msg io.WriterTo) error

func(f SendFunc) Send(from string, to []string, msg io.WriterTo) error

type Sender interface {
  Send(from string, to []string, msg io.WriterTo) error
}
```


