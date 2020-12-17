# Mailscript templates: shared address for team

You can use this template to setup shared access to an address you control. Addresses use `scoped keys` (`read`/`write`) to be able to listen in to incoming messages and send messages from such address. By setting up redirects from an address an providing a `write-scoped key` to your peers you can easily setup a shared address.

## Workflow

```yml
version: "0.1"
addresses:
  shared@username.mailscript.com:
    keys:
      - name: Shared write key
        read: false
        write: true
      - name: owner
        read: true
        write: true
accessories:
  - name: shared@username.mailscript.com
    type: mailscript-email
    address: shared@username.mailscript.com
    key: owner
workflows:
  - name: redirect to external
    trigger:
      accessory: shared@username.mailscript.com
      config:
        criterias: []
    actions:
      - accessory: shared@username.mailscript.com
        config:
          type: alias
          alias: address@external.com
```

## Manual setup

Claim an address on your username's subdomain:

```sh
mailscript addresses:add --address shared@username.mailscript.com
```

Set redirects to external (existing) addresses (you'll have to verify ownership of such addresses):

```sh
mailscript workflows:add \
  --name "redirect to external" \
  --trigger shared@username.mailscript.com \
  --alias address@external.com
```

Create and get write key to share with peers:

```sh
mailscript keys:add \
  --address shared@username.mailscript.com \
  --name "Shared write key" \
  --write

mailscript keys:list --address shared@username.mailscript.com
```

## SMTP out configuration

Keys with `write` scope can be used as passwords for sending messages via Mailscript's `smtp` gateway. The following configuration parameters are needed to setup a Gmail client to be able to "send as" from a Mailscript address:

```
host: smtp.mailscript.com
port: 465
user: full-address@mailscript.com
pass: key-with-write-scope
```
