# Brazil-Broker-catalog
A simple webpage that lists all brokers provided by BrazilAPI.
This project consists of an API built with Node-RED, responsible for making a request to the following endpoint:

https://brasilapi.com.br/api/cvm/corretoras/v1

From each broker, the API extracts:
- nome_social (corporate name)
- municipio (city)
- CNPJ (Brazilian company ID)

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
function space(str, tamanho){
  // function responsible for calculating and adding the number of spaces until it reaches the specified value
    return str + " ".repeat(Math.max(0,tamanho-str.length));
}

let brokersArray = msg.payload;
brokersArray = JSON.parse(brokersArray);

// Finds the length of the longest nome_social
let nameMaxLength = Math.max(...brokersArray.map(item => item.nome_social.length));

// Finds the length of the longest city
let cityMaxLength = Math.max(...brokersArray.map(item => item.municipio.length));

// Extracting only the social name, municipality, and CNPJ of each brokerage firm
let formattedList = brokersArray.map(item => {
  let paddedName = space(item.nome_social, nameMaxLength);  // function that adds spaces so the name stays aligned
  let paddedMunicipality = space(item.municipio,cityMaxLength);
  return `<li>${paddedName}  -  ${paddedMunicipality}  /  ${item.cnpj} </li>`;
}).join("");

msg.payload = formattedList;
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
      .titulo{
        display: flex;
        justify-content: center;
      }
    </style>
  </head>
  <body>
    <div class=titulo>
      <h1>CATÁLOGO DE CORRETORAS</h1>
    </div>

    <ul style="white-space: pre; font-family: monospace;"> 
      <li>
        <strong>CORRETORA                                                                                  -  MUNICÍPIO              /   CNPJ </strong>
        </li>
    {{{payload}}}<!-- 3 curly braces to render HTML from the payload }} -->
    </ul>

  </body>
</html>
```

### HTTP RESPONSE
Sends the final HTML response back to the client that accessed the endpoint.
