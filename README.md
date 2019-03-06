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
```


