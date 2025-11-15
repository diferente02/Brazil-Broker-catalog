# Brazil-Broker-catalog [![platform](https://img.shields.io/badge/plataform-Node--RED-red)](https://nodered.org)
This project consists of an API built with Node-RED, responsible for making a request to the following endpoint:
https://brasilapi.com.br/api/cvm/corretoras/v1 and show a simple webpage that lists all brokers provided by BrazilAPI.From each broker, the API extracts:
- nome_social (corporate name)
- municipio (city)
- CNPJ (Brazilian company ID)

The API then displays this information on a dynamically generated HTML page.

<img width="1323" height="607" alt="Image" src="https://github.com/user-attachments/assets/01d63c47-dd1c-4957-bb56-32381003e9d4" />

## Table of Contents

- [Installation](#installation)
- [Usage](#Usage)
- [Flow Structure](#Flow-Structure)
- [HTTP In](#HTTP-In)
- [HTTP request](#HTTP-request)
- [FUNCTION](#FUNCTION)
- [TEMPLATE](#TEMPLATE)
- [HTTP RESPONSE](#HTTP-RESPONSE)

## Installation
1. Install Node.js
Download it from the official website:
[Nodejs](https://nodejs.org/en)

   you can check the version using the command
  ```bash
  node --version
  ```
2.Install Node-RED

With Node.js installed, run:

```bash
npm install -g --unsafe-perm node-red
```

## Usage
1.Start Node-Red
  In the terminal:
  ```bash
  node-red
  ```
2.Then open the editor in your browser:
  http://localhost:1880
  or
  http://127.0.0.1:1880/

3.Importing the flow
 
   &nbsp;&nbsp;&nbsp;&nbsp;3.1. In the top-right corner of the screen, click the menu button (☰).
   
   &nbsp;&nbsp;&nbsp;&nbsp;3.2. Select Import from the dropdown menu.
   
   &nbsp;&nbsp;&nbsp;&nbsp;3.3. A text window will appear. Paste the JSON that you did make the download.
   
   &nbsp;&nbsp;&nbsp;&nbsp;3.4. Click Import to load the flow into your workspace.
   
   &nbsp;&nbsp;&nbsp;&nbsp;3.5. Position the nodes as needed and then click Deploy to apply the changes.

## Flow Structure
The flow is divided into 5 main nodes

<img width="923" height="294" alt=" the 5 nos of the flow" src="https://github.com/user-attachments/assets/c9addc78-01de-4131-8c87-f0ad3879b391" />


### HTTP In 
<img width="315" height="45" alt="Image" src="https://github.com/user-attachments/assets/be7c9732-2860-49f7-a6cd-58c9116d562e" />

Creates a simple web service.

to the see the webpage write this link in your browse: http://localhost:1880/Brazil_Broker_API
 

###  HTTP request 
<img width="251" height="45" alt="Image" src="https://github.com/user-attachments/assets/f27f6d11-1110-491a-84b2-ba8cc895c491" />

Makes the HTTP call to the BrazilAPI endpoint:

https://brasilapi.com.br/api/cvm/corretoras/v1

that return:
```
{
    "cnpj": "76621457000185",
    "type": "CORRETORAS",
    "nome_social": "4UM DTVM S.A.",
    "nome_comercial": "4UM INVESTIMENTOS",
    "status": "CANCELADA",
    "email": "controle@4um.com.br",
    "telefone": "33519966",
    "cep": "80420210",
    "pais": "BRASIL",
    "uf": "PR",
    "municipio": "CURITIBA",
    "bairro": "CENTRO",
    "complemento": "4º ANDAR",
    "logradouro": "R. VISCONDE DO RIO BRANCO 1488",
    "data_patrimonio_liquido": "2005-12-31",
    "valor_patrimonio_liquido": "4228660.18",
    "codigo_cvm": "2275",
    "data_inicio_situacao": "2006-10-05",
    "data_registro": "1968-01-15"
  },
```

### FUNCTION 
<img width="150" height="45" alt="Image" src="https://github.com/user-attachments/assets/5ebcdfda-b1b3-4959-94d0-4247d706cfb7" />

Processes the incoming payload, extracts the fields: "nome_social", "municipio", "CNPJ" and formats the HTML list.

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
<img width="150" height="45" alt="Image" src="https://github.com/user-attachments/assets/1c662e9c-4106-4792-94b8-6f757ea6b9e4" />

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
<img width="283" height="90" alt="Image" src="https://github.com/user-attachments/assets/b548d1ca-9095-4f7d-9f3a-73fb49779ef9" />

Sends the final HTML response back to the client that accessed.
