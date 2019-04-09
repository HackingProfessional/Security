---
layout: post
title: "Como compartir archivos a través de HTTP"
excerpt: "Aprende a compartir rápidamente archivos y carpetas a través de HTTP en Linux"
image: "https://hackingprofessional.github.io/Security/images/shareHttpLinux.png"
tags: 
  - Aprendiendo Python
  - Aprendiendo Ruby
  - Aprendiendo NodeJs
---

Durante el proceso para comprometer totalmente un servidor victima, es muy probable que tengamos que subir a este exploits o scripts que automaticen ciertos procesos.
En el dia de hoy, vamos aprender diferentes métodos para servir un solo archivo o un directorio completo con otros sistemas en su red de área local a través de un navegador web.

## Como Servir archivos y carpetas sobre HTTP en Linux
### Python

```python
#Python 3.x
python3 -m http.server

#Python 2.x
python -m SimpleHTTPServer 6969
```

### Ruby

```ruby
ruby -run -e httpd . -p 6969
```

### NodeJs
Para levantar el servidor HTTP con NodeJs debemos instalar la siguiente libreria:
```shell
npm install http-server -g
```
Luego corremos lo siguiente:
```javascript
http-server -p 6969
```

### PHP

```php
php -S localhost:6969 -t .
```

