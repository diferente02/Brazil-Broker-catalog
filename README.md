# Brazil-Broker-catalog
  A simple webpage that lists all brokers provided by BrazilAPI

# Broker-Catalog
This project consists of an API built with Node-RED, responsible for making a request to the following endpoint:

https://brasilapi.com.br/api/cvm/corretoras/v1

From each broker, the API extracts:
- nom_social(corporate name)
- municipio(city)
- CNPJ(Brazilian company ID)

The API then displays this information on a dynamically generated HTML page.
<img src="images/template html api.png" alt="site gerado pela API">


# NODE-RED

This project uses NODE-RED, a powerful low-code tool for visually building automation flows.

## 🚀 Installation
### Install Node.js
Download it from the official website:
[Nodejs.org](https://nodejs.org/en)

After installation, verify the version:
```bash
node --version
```
### Install Node-RED
With Node.js installed, run:

```bash
npm install -g --unsafe-perm node-red
```

## Usage
### Start Node-Red
In the terminal:
```bash
node-red
```
Then open the editor in your browser:
http://localhost:1880

or

http://127.0.0.1:1880/

### Importação do fluxo
 

<img src="images/Importacao_Tres_barrinhas.png" alt="Imagem 1" width="150">
<img src="images/Importacao.png" alt="Imagem 2" width="150">
<img src="images/Importacao_Arquivo.png" alt="Imagem 3" width="150">


## 🧩 Flow Structure
The flow is divided into 5 main nodes

<img src="images/Fluxo.png" alt="Tutorial de como importar o fluxo, imagem 1">

### HTTP In
Creates a simple web service.

Endpoint:
http://localhost:1880/Brazil_Broker_API
write this link in your browse to the see the webpage.

###  HTTP request
Makes the HTTP call to the BrazilAPI endpoint:

https://brasilapi.com.br/api/cvm/corretoras/v1

### FUNCTION
Processes the incoming payload, extracts the desired fields, and formats the HTML list.

```Javascript
let corretoras = msg.payload;
corretoras = JSON.parse(corretoras);

// Extraindo apenas o nome social, municipio e o cnpj de cada corretora
let listaFormatada = corretoras.map(item => {
   return `${item.nome_social}  -  ${item.municipio}  /  ${item.cnpj}`;
});

// Formatando a mensagem como lista 
let htmlLista = "<ul>";
listaFormatada.forEach(item =>{
   htmlLista += `<li>${item}</li>`;
});
htmlLista += "</ul>";

msg.payload = htmlLista;
return msg;
```

### TEMPLATE
Generates the final HTML page using the payload content.
```html
<html>
  <head>
    <title>Catalogo de corretoras</title>
    <style>
      body {font-family:Arial, sans-serif; padding:20px; background-color:#f2f2f2;}
      h1 {color: #007acc;}
      ul { list-style-type:none; padding:0;}
      li {margin: 5px 50px; padding: 5px; background-color:white; border-radius:4px;}
    </style>
  </head>
  <body>
    <h1>Lista de corretoras</h1>
    <p>{{{payload}}}</p> {{!-- 3 chaves para renderizar HTML vindo do payload --}}
  </body>
</html>
```

### HTTP RESPONSE
Sends the final HTML response back to the client that accessed the endpoint.
