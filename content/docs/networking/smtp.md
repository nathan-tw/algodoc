---
title: Email & SMTP
bookToc: false
---


# Email & SMTP

寄信是一個看似平常，但手續繁複的流程。在網路世界中規範了信件的格式，例如必須包含收件人、寄件人等等，同時也規範了用戶端與伺服器端連線的方式，這個協定稱作 SMTP (Simple Mail Transfer Protocol).

## SMTP

整個寄信流程可以簡單分成四個步驟。

1. **建立TCP連接**：SMTP連接始於建立一個TCP連接，SMTP服務器使用TCP端口25進行監聽。客戶端使用TCP協議通過這個端口連接到SMTP服務器。

2. **發送郵件**：一旦TCP連接建立成功，客戶端就會向SMTP服務器發送郵件。郵件的內容可以是純文本，也可以是HTML格式，通過SMTP協議傳輸的郵件包含了郵件標題、收件人、寄件人、郵件內容等信息。

3. **服務器回應**：SMTP服務器接收到客戶端發送的郵件後，會對其進行驗證，例如檢查郵件地址的格式是否正確。如果郵件格式正確，SMTP服務器就會回應一個確認信號，表示它已經接收到了郵件，並且開始將郵件發送到收件人的郵箱中，如果不正確則可以根據伺服器端設定，常見的做法會將信根據return path返回給對方。

4. **關閉SMTP連接**：一旦郵件被成功發送到收件人的郵箱中，SMTP服務器會關閉與客戶端的SMTP連接。

通常在建立TCP連接後，客戶端會先向SMTP服務器發送HELO（或EHLO）命令，以表明客戶端的身份。因此，在SMTP連接時，通常需要使用HELO命令。

HELO命令用於向SMTP服務器發送客戶端的主機名稱或IP地址，以表明客戶端的身份。SMTP服務器接收到HELO命令後，會向客戶端發送一條歡迎消息，以確認客戶端的身份。EHLO命令是HELO的擴展版本，它支持更多的SMTP擴展功能，例如STARTTLS和AUTH等。

因此，在SMTP連接的過程中，通常需要先使用HELO（或EHLO）命令進行客戶端身份的驗證，然後再進行郵件發送等操作。這樣可以確保SMTP服務器能夠識別客戶端身份，並避免郵件被誤認為垃圾郵件或未經授權的郵件。

    S: 220 smtp.example.com ESMTP Postfix
    C: HELO relay.example.org
    S: 250 Hello relay.example.org, I am glad to meet you
    C: MAIL FROM:<bob@example.org>
    S: 250 Ok
    C: RCPT TO:<alice@example.com>
    S: 250 Ok
    C: RCPT TO:<theboss@example.com>
    S: 250 Ok
    C: DATA
    S: 354 End data with <CR><LF>.<CR><LF>

## 使用 `nslookup` 找到郵件伺服器

在DNS紀錄中有一個類型是紀錄著郵件交換的，稱之為 `MX Record` (mail exchanger record)，可以透過 `nslookup` 的過程指定 `-type=mx` 找到對應的郵件伺服器。

    $ nslookup -type=mx gmail.com
    Server:		168.95.1.1
    Address:	168.95.1.1#53

    Non-authoritative answer:
    gmail.com	mail exchanger = 20 alt2.gmail-smtp-in.l.google.com.
    gmail.com	mail exchanger = 40 alt4.gmail-smtp-in.l.google.com.
    gmail.com	mail exchanger = 10 alt1.gmail-smtp-in.l.google.com.
    gmail.com	mail exchanger = 5 gmail-smtp-in.l.google.com.
    gmail.com	mail exchanger = 30 alt3.gmail-smtp-in.l.google.com.

## 根據 SMTP 寄信前能做到的事

1. 確認對方的郵件伺服器存在
2. 確認對方的郵箱存在
3. 確認對方郵件伺服器支援的功能 (e.g., TLS)

### ESMTP
Extension discovery mechanism
如果對方server支援 ESMTP (Enhanced SMTP)，在 HELO 階段可以用 EHLO 替代，這樣可以取得 server 支援的 extension，因此可以透過這個方式檢查對方server是否支持 TLS 等等。

    S: 220 smtp2.example.com ESMTP Postfix
    C: EHLO bob.example.org
    S: 250-smtp2.example.com Hello bob.example.org [192.0.2.201]
    S: 250-SIZE 14680064
    S: 250-PIPELINING
    S: 250 HELP

目前常用的 commands有

- **8BITMIME** – 8 bit data transmission, RFC 6152
- **ATRN** – Authenticated TURN for On-Demand Mail Relay, RFC 2645
- **AUTH** – Authenticated SMTP, RFC 4954
- **CHUNKING** – Chunking, RFC 3030
- **DSN** – Delivery status notification, RFC 3461 (See Variable envelope return path)
- **ETRN** – Extended version of remote message queue starting command TURN, RFC 1985
- **HELP** – Supply helpful information, RFC 821
- **PIPELINING** – Command pipelining, RFC 2920
- **SIZE** – Message size declaration, RFC 1870
- **STARTTLS** – Transport Layer Security, RFC 3207 (2002)
- **SMTPUTF8** – Allow UTF-8 encoding in mailbox names and header fields, RFC 6531