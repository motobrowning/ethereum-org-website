---
title: 'Contrato inteligente de Hello World para principiantes: Fullstack'
description: Tutorial introductorio para redactar e implementar un contrato inteligente sencillo en Ethereum.
author: "nstrike2"
tags:
  - "solidity"
  - "hardhat"
  - "alchemy"
  - "contratos inteligentes"
  - "implementación"
  - "blockexplorer"
  - "frontend"
  - "transacciones"
skill: beginner
lang: es
published: 2021-10-25
---

Si es nuevo en el desarrollo de la cadena de bloques y no sabe por dónde empezar o cómo implementar e interactuar con contratos inteligentes, esta guía está hecha a su medida. Exploraremos la creación e implementación de un contrato simple e inteligente en la red de prueba de Goerli utilizando [MetaMask](https://metamask.io), [Solidity](https://docs.soliditylang.org/en/v0.8.0/), [Hardhat](https://hardhat.org) y [Alchemy](https://alchemyapi.io/eth).

Necesitará una cuenta de Alchemy para completar este tutorial. [Regístrese para obtener una cuenta gratuita](https://www.alchemy.com/).

En cualquier momento que le surjan dudas, ¡no dude en ponerse en contacto con el canal [Discord de Alchemy](https://discord.gg/gWuC7zB).

## Parte 1: Cree e implemente su contrato inteligente usando Hardhat {#part-1}

### Conéctarse a la red de Ethereum {#connect-to-the-ethereum-network}

Hay muchas maneras de hacer solicitudes a la cadena Ethereum. Para simplificar, usaremos una cuenta gratuita en Alchemy, una plataforma de desarrollo de cadena de bloques y una API que nos permite comunicarnos con la cadena Ethereum sin ejecutar un nodo nosotros mismos. Alchemy también tiene herramientas de desarrollo para el control y el análisis. Las abordaremos en este tutorial y así entenderemos los entresijos de nuestra implementación de contratos inteligentes.

### Cómo crear su aplicación y clave de API {#create-your-app-and-api-key}

Una vez que haya creado una cuenta de Alchemy, puede generar una clave de API con una aplicación. Esto le permitirá hacer solicitudes a la red de pruebas de Goerli. Si no está familiarizado con las redes de prueba, puede [leer la guía de Alchemy para elegir una red](https://docs.alchemyapi.io/guides/choosing-a-network).

En el panel de Alchemy, busque el menú desplegable **Apps** en la barra de navegación y haga clic en **Create App**.

![Crear la aplicación Hola, mundo](./hello-world-create-app.png)

Dele a su aplicación el nombre '_Hello World_' y escriba una breve descripción. Seleccione **Staging** como su entorno y **Goerli** como su red.

![Crear App visualizar Hola, mundo](./create-app-view-hello-world.png)

_Nota: asegúrate de seleccionar **Goerli**, o este tutorial no funcionará._

Haga clic en **Create app**. Su aplicación aparecerá en la siguiente tabla.

### Cómo crear una cuenta en Ethereum {#create-an-ethereum-account}

Necesita tener una cuenta de Ethereum para enviar y recibir transacciones. Utilizaremos MetaMask, una cartera virtual en el navegador que permite a los usuarios gestionar la dirección de su cuenta de Ethereum.

Puede descargar y crear una cuenta Metamask gratis [aquí](https://metamask.io/download.html). Cuando esté creando una cuenta, o si ya tiene una, asegúrese de cambiar a la “red de prueba Goerli” en la parte superior derecha (para no operar con dinero real).

### Paso 4: Añadir ether de un faucet {#step-4-add-ether-from-a-faucet}

Para implementar su contrato inteligente en la red de prueba, necesitará algunos ETH falsos. Para obtener ETH en la red Goerli, vaya a un grifo de Goerli e introduzca la dirección de su cuenta de Goerli. Observe que los grifos de Goerli pueden ser poco fiables recientemente. En la [página de prueba de redes](/developers/docs/networks/#goerli) verá un listado de opciones para probar:

_Nota: debido a la congestión de la red, esto puede llevar un tiempo. _ ``

### Paso 5: Comprobar su balance {#step-5-check-your-balance}

Para volver a comprobar que hay ETH en su cartera, hagamos una solicitud [eth_getBalance](https://docs.alchemyapi.io/alchemy/documentation/alchemy-api-reference/json-rpc#eth_getbalance) usando la herramienta de compositor de [Alchemy](https://composer.alchemyapi.io/?composer_state=%7B%22network%22%3A0%2C%22methodName%22%3A%22eth_getBalance%22%2C%22paramValues%22%3A%5B%22%22%2C%22latest%22%5D%7D). Esto devolverá la cantidad de ETH a nuestra cartera. Si desea ampliar esta información, eche un vistazo al breve tutorial de [Alchemy sobre cómo usar la herramienta de compositor](https://youtu.be/r6sjRxBZJuU).

Introduzca la dirección de su cuenta de MetaMask y haga clic en **Send request**. Verás una respuesta que se parece al fragmento de código a continuación.

```json
{ "jsonrpc": "2.0", "id": 0, "result": "0x2B5E3AF16B1880000" }
```

> _Nota: este resultado está en wei, no en ETH. Wei se usa como la denominación más pequeña de Ether._

¡Fiu! Nuestro dinero de prueba está ahí sano y salvo.

### Paso 6: Iniciar nuestro proyecto {#step-6-initialize-our-project}

En primer lugar, tendremos que crear una carpeta para nuestro proyecto. Vaya a su línea de comandos e introduzca lo siguiente.

```
mkdir hello-world
cd hello-world
```

Ahora que estamos dentro de nuestra carpeta de proyecto, usaremos `npm init` a fin de inicializar el proyecto.

> Si aún no tiene npm instalado, siga [estas instrucciones para instalar Node.js y npm](https://docs.alchemyapi.io/alchemy/guides/alchemy-for-macs#1-install-nodejs-and-npm).

Para el propósito de este tutorial, no importa cómo responda a las preguntas de inicialización. Así respondimos nosotros, a modo de referencia:

```
package name: (hello-world)
version: (1.0.0)
description: hello world smart contract
entry point: (index.js)
test command:
git repository:
keywords:
author:
license: (ISC)

About to write to /Users/.../.../.../hello-world/package.json:

{
   "name": "hello-world",
   "version": "1.0.0",
   "description": "hello world smart contract",
   "main": "index.js",
   "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
   },
   "author": "",
   "license": "ISC"
}
```

Apruebe el package.json y listo.

### Paso 7: Descargar Hardhat {#step-7-download-hardhat}

Hardhat es un entorno de desarrollo para compilar, implementar, probar y depurar su software de Ethereum. Ayuda a los desarrolladores cuando crean contratos inteligentes y dApps localmente antes de la implementación en la cadena real.

Dentro de nuestro proyecto `hello-world`, ejecute:

```
npm install --save-dev hardhat
```

Revise esta página para obtener más información acerca de las [intrucciones de instalación](https://hardhat.org/getting-started/#overview).

### Paso 8: Crear proyecto Hardhat {#step-8-create-hardhat-project}

Dentro de nuestra carpeta de proyectos `hello-world`, ejecute:

```
npx hardhat
```

Debería aparecer un mensaje de bienvenida y la opción de seleccionar lo que desee hacer. Seleccione «create an empty hardhat.config.js»:

```
888    888                      888 888               888
888    888                      888 888               888
888    888                      888 888               888
8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
888    888 .d888888 888    888  888 888  888 .d888888 888
888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888

👷 Welcome to Hardhat v2.0.11 👷‍

What do you want to do? …
Create a sample project
❯ Create an empty hardhat.config.js
Quit
```

Esto generará un archivo `hardhat.config.js` en el proyecto. Lo utilizaremos más adelante en el tutorial para especificar la configuración de nuestro proyecto.

### Paso 9: Añadir carpetas de proyecto {#step-9-add-project-folders}

Para mantener el proyecto organizado, vamos a crear dos carpetas nuevas. En la línea de comandos, vaya al directorio raíz de su proyecto `hello-world` y escriba:

```
mkdir contracts
mkdir scripts
```

- `contratos/` es donde mantendremos nuestro archivo de código del contrato inteligente Hola, mundo
- `scripts/` es donde mantendremos los scripts para implementar e interactuar con nuestro contrato

### Paso 10: Escribir nuestro contrato {#step-10-write-our-contract}

Puede que se esté preguntando que cuándo vamos a escribir el código. ¡Ahora es el momento!

Abra el proyecto hello-world en su editor favorito. Los contratos inteligentes se suelen escribir más comunmente en Solidity, que usaremos para escribir nuestro contrato inteligente

1. Vaya a la carpeta `contratos` y cree un nuevo archivo llamado `HelloWorld.sol`
2. A continuación se muestra un ejemplo del contrato inteligente de Hello World que usaremos para este tutorial. Copie el contenido a continuación en el archivo `HelloWorld.sol`.

_Nota: asegúrese de leer los comentarios para entender lo que hace este contrato._

```
// Especifica la versión de Solidity, utilizando la versión semántica.
// Learn more: https://solidity.readthedocs.io/en/v0.5.10/layout-of-source-files.html#pragma
pragma solidity >=0.7.3;

// Defines a contract named `HelloWorld`.
// Un contrato es una colección de funciones y datos (su estado). Una vez desplegado, un contrato reside en una dirección específica en la blockchain de Ethereum. Learn more: https://solidity.readthedocs.io/en/v0.5.10/structure-of-a-contract.html
contract HelloWorld {

   //Emitted when update function is called
   //Smart contract events are a way for your contract to communicate that something happened on the blockchain to your app front-end, which can be 'listening' for certain events and take action when they happen.
   event UpdatedMessages(string oldStr, string newStr);

   // Declares a state variable `message` of type `string`.
   // Las variables de estado son variables cuyos valores se almacenan permanentemente en el almacenamiento del contrato. The keyword `public` makes variables accessible from outside a contract and creates a function that other contracts or clients can call to access the value.
   string public message;

   // Similar to many class-based object-oriented languages, a constructor is a special function that is only executed upon contract creation.
   // Los constructores se utilizan para inicializar los datos del contrato. Learn more:https://solidity.readthedocs.io/en/v0.5.10/contracts.html#constructors
   constructor(string memory initMessage) {

      // Accepts a string argument `initMessage` and sets the value into the contract's `message` storage variable).
      message = initMessage;
   }

   // A public function that accepts a string argument and updates the `message` storage variable.
   function update(string memory newMessage) public {
      string memory oldMsg = message;
      message = newMessage;
      emit UpdatedMessages(oldMsg, newMessage);
   }
}
```

Este es un contrato inteligente básico que almacena un mensaje en el momento de la creación. Se puede actualizar activando la función `update`.

### Paso 11: Conectar MetaMask & Alchemy a su proyecto {#step-11-connect-metamask-alchemy-to-your-project}

Hemos creado una billetera de Metamask, una cuenta de Alchemy y escrito nuestro contrato inteligente, ahora es momento de conectarlos entre sí.

Cada transacción enviada desde su cartera requiere una firma con su clave privada única. Para proporcionar este permiso a nuestro programa, podemos almacenar de forma segura nuestra clave privada en un archivo de entorno. También almacenaremos una clave de API para Alchemy aquí.

> Si quiere ahondar sobre el envío de transacciones, consulte [este tutorial](https://docs.alchemyapi.io/alchemy/tutorials/sending-transactions-using-web3-and-alchemy) sobre el envío de transacciones usando web3.

Primero, instale el paquete dotenv en su directorio de proyecto:

```
npm install dotenv --save
```

A continuación, cree un archivo `.env` en el directorio raíz del proyecto. Añade tu clave privada de MetaMask y la URL de la API de HTTP Alchemy.

Su archivo de entorno debe llamarse `.env` o no se reconocerá como un archivo de entorno.

No lo nombre `process.env` o `.env-custom` ni nada más.

- Siga [estas instrucciones](https://metamask.zendesk.com/hc/en-us/articles/360015289632-How-to-Export-an-Account-Private-Key) para exportar su llave privada
- Abajo se le indica cómo obtener la API URL de HTTP Alchemy

![](./get-alchemy-api-key.gif)

Su `.env` debería verse así:

```
API_URL = "https://eth-goerli.alchemyapi.io/v2/your-api-key"
PRIVATE_KEY = "tu-clave privada-metamask"
```

Para conectarlos efectivamente a nuestro código, vincularemos estas variables en nuestro `hardhat.config.js` en el paso 13.

### Paso 12: Instalar Ethers.js {#step-12-install-ethersjs}

Ethers.js es una biblioteca que facilita la interacción y la realización de solicitudes a Ethereum agrupando [métodos JSON-RPC estándar](https://docs.alchemyapi.io/alchemy/documentation/alchemy-api-reference/json-rpc) con métodos más fáciles para el usuario.

Hardhat nos permite integrar [plugins](https://hardhat.org/plugins/) para obtener herramientas adicionales y una funcionalidad ampliada. Aprovecharemos el complemento [Ethers plugin](https://hardhat.org/plugins/nomiclabs-hardhat-ethers.html) para la implementación por contrato.

En el directorio de su proyecto, teclee:

```bash
npm install --save-dev @nomiclabs/hardhat-ethers "ethers@^5.0.0"
```

### Paso 13: Actualizar hardhat.config.js {#step-13-update-hardhat.configjs}

Hasta ahora hemos añadido varias dependencias y plugins, por lo que ahora necesitamos actualizar `hardhat.config.js` para que nuestro proyecto sepa de todas ellas.

Actualice su `hardhat.config.js` para que muestre el siguiente texto:

```javascript
/**
 * @type import('hardhat/config').HardhatUserConfig
 */

require("dotenv").config()
require("@nomiclabs/hardhat-ethers")

const { API_URL, PRIVATE_KEY } = process.env

module.exports = {
  solidity: "0.7.3",
  defaultNetwork: "goerli",
  networks: {
    hardhat: {},
    goerli: {
      url: API_URL,
      accounts: [`0x${PRIVATE_KEY}`],
    },
  },
}
```

### Paso 14: Compilar nuestro contrato {#step-14-compile-our-contract}

Para asegurarnos de que todo funciona correctamente hasta ahora, compilemos nuestro contrato. La función `compile` está incluida dentro de las funciones por defecto de hardhat.

Desde la linea de comandos, ejecute:

```bash
npx hardhat compile
```

Es posible que reciba una advertencia sobre el `identificador de licencia SPDX no proporcionado en el archivo de origen`, pero no hay necesidad de preocuparse por eso, ¡esperemos que todo lo demás se vea bien! Si no es así, siempre puede escribir un mensaje en [Alchemy discord](https://discord.gg/u72VCg3).

### Paso 15: Escribir nuestro script de despliegue {#step-15-write-our-deploy-script}

Ahora que nuestro contrato está escrito y nuestro archivo de configuración está listo, es momento de escribir nuestro script de implementación del contrato.

Vaya a la carpeta `scripts/` y cree un nuevo archivo llamado `deploy.js`, agregando los siguientes contenidos:

```javascript
async function main() {
  const HelloWorld = await ethers.getContractFactory("HelloWorld")

  // Start deployment, returning a promise that resolves to a contract object
  const hello_world = await HelloWorld.deploy("Hello World!")
  console.log("Contract deployed to address:", hello_world.address)
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error)
    process.exit(1)
  })
```

Hardhat hace un trabajo increíble al explicar lo que hace cada una de estas líneas de código en su [tutorial de contratos](https://hardhat.org/tutorial/testing-contracts.html#writing-tests), aquí hemos asumido sus explicaciones.

```javascript
const HelloWorld = await ethers.getContractFactory("HelloWorld")
```

Una `ContractFactory` en ethers.js es una abstracción utilizada para implementar nuevos contratos inteligentes, por lo que `HelloWorld` aquí es una [factory](https://en.wikipedia.org/wiki/Factory_(object-oriented_programming)) para las instancias de nuestro contrato de Hello World. Cuando se utiliza el complemento `hardhat-ethers` `ContractFactory` y `Contract`, las instancias se conectan al primer firmante (propietario) de forma predeterminada.

```javascript
const hello_world = await HelloWorld.deploy()
```

Llamar a `deploy()` en un `ContractFactory` iniciará el despliegue y devolverá un `Promesa` que se resuelve en un objeto `Contract`. Este es el objeto que tiene un método para cada una de nuestras funciones de contrato inteligente.

### Paso 16: Desplegar nuestro contrato {#step-16-deploy-our-contract}

¡Por fin estamos listos para desplegar nuestro contrato inteligente! Desde la linea de comandos, ejecute:

```bash
hardhat npx ejecuta scripts/deploy.js --network goerli
```

Debería mostrarse algo parecido a esto:

```bash
Contract deployed to address: 0x6cd7d44516a20882cEa2DE9f205bF401c0d23570
```

**Por favor, guarde esta dirección**. Lo usaremos más adelante en el tutorial.

Si vamos a la dirección [Ropsten etherscan](https://goerli.etherscan.io) y buscamos la dirección de nuestro contrato, podremos comprobar que se ha desplegado correctamente. El objeto de la transacción tendrá un aspecto parecido a esto:

![](./etherscan-contract.png)

La dirección `from` debe coincidir con la dirección de su cuenta de MetaMask y la dirección `To` dirá **Contrcat creation**. Si hacemos clic en la transacción, veremos la dirección de nuestro contrato en el campo `to`.

![](./etherscan-transaction.png)

¡Felicidades! Acabas de implementar un contrato inteligente en una red de pruebas de Ethereum.

Para entender lo que está pasando internamente, vayamos a la pestaña de Explorer en nuestro [panel Alchemy](https://dashboard.alchemyapi.io/explorer). Si tienes varias aplicaciones de Alchemy, asegúrate de filtrar por aplicación y selecciona **Hello World**.

![](./hello-world-explorer.png)

Aquí verás un puñado de métodos JSON-RPC que Hardhat/Ethers hizo bajo el capó para nosotros cuando llamamos a la función `.deploy()`. Dos métodos importantes aquí son [`eth_sendRawTransaction`](https://docs.alchemyapi.io/alchemy/documentation/alchemy-api-reference/json-rpc#eth_sendrawtransaction), que es la solicitud para escribir nuestro contrato en la cadena Goerli, y [`eth_getTransactionByHash`](https://docs.alchemyapi.io/alchemy/documentation/alchemy-api-reference/json-rpc#eth_gettransactionbyhash), que es una solicitud para leer información sobre nuestra transacción dado el hash. Para obtener más información sobre el envío de transacciones, consulte [nuestro tutorial sobre el envío de transacciones utilizando Web3](/developers/tutorials/sending-transactions-using-web3-and-alchemy/).

## Parte 2: Interactúa con tu contrato inteligente {#part-2-interact-with-your-smart-contract}

Ahora que hemos implementado con éxito un contrato inteligente en la red Goerli, aprendamos a interactuar con él.

### Crear un archivo interact.js {#create-a-interactjs-file}

Este es el archivo donde escribiremos nuestro script de interacción. Utilizaremos la biblioteca Ethers.js que instalaste anteriormente en la Parte 1.

Dentro de la carpeta `scripts/`, cree un nuevo archivo llamado `interact.js` y agregue el siguiente código:

```javascript
// interact.js

const API_KEY = process.env.API_KEY
const PRIVATE_KEY = process.env.PRIVATE_KEY
const CONTRACT_ADDRESS = process.env.CONTRACT_ADDRESS
```

### Actualiza tu archivo .env {#update-your-env-file}

Utilizaremos nuevas variables de entorno, por lo que tenemos que definirlas en el archivo `.env` que [creamos anteriormente](#step-11-connect-metamask-&-alchemy-to-your-project).

Tendremos que añadir una definición para nuestra Alchemy `API_KEY` y la `CONTRACT_ADDRESS` donde se desplegó su contrato inteligente.

Su archivo `.env` debería tener un aspecto similar a esto:

```bash
# .env

API_URL = "https://eth-goerli.alchemyapi.io/v2/<your-api-key>"
API_KEY = "<your-api-key>"
PRIVATE_KEY = "<your-metamask-private-key>"
CONTRACT_ADDRESS = "0x<your contract address>"
```

### Toma tu contrato ABI {#grab-your-contract-ABI}

Nuestro contrato [ABI (Interfaz Binaria de Aplicación)](/glossary/#abi) es la interfaz para interactuar con nuestro contrato inteligente. Hardhat genera automáticamente un ABI y lo guarda en `HelloWorld.json`. Para usar el ABI, tendremos que analizar el contenido añadiendo las siguientes líneas de código a nuestro archivo `interact.js`:

```javascript
// interact.js
const contract = require("../artifacts/contracts/HelloWorld.sol/HelloWorld.json")
```

Si quiere ver la ABI puede hacerlo en su propia consola:

```javascript
console.log(JSON.stringify(contract.abi))
```

Para ver su ABI impreso en la consola, vaya a su terminal y ejecute:

```bash
npx hardhat run scripts/interact.js
```

### Crea una instancia de tu contrato {#create-an-instance-of-your-contract}

Para interactuar con nuestro contrato, necesitamos crear una instancia de contrato en nuestro código. Para hacerlo con Ethers.js, tendremos que trabajar con tres conceptos:

1. Proveedor: un proveedor de nodos que te da acceso de lectura y escritura a la cadena de bloques
2. Firmante - representa una cuenta de Ethereum que puede firmar transacciones
3. Contrato: un objeto Ethers.js que representa un contrato específico desplegado en cadena

Utilizaremos el contrato ABI del paso anterior para crear nuestra instancia del contrato:

```javascript
// interact.js

// Provider
const alchemyProvider = new ethers.providers.AlchemyProvider(
  (network = "goerli"),
  API_KEY
)

// Signer
const signer = new ethers.Wallet(PRIVATE_KEY, alchemyProvider)

// Contract
const helloWorldContract = new ethers.Contract(
  CONTRACT_ADDRESS,
  contract.abi,
  signer
)
```

Obtenga más información sobre proveedores, firmantes y contratos en la documentación de [ethers.js](https://docs.ethers.io/v5/).

### Lee el mensaje de inicio {#read-the-init-message}

Recuerda cuando implementamos nuestro contrato con el `initMessage = "¡Hola mundo!" `? Ahora vamos a leer ese mensaje almacenado en nuestro contrato inteligente e imprimirlo en la consola.

En JavaScript, las funciones asíncronas se utilizan al interactuar con las redes. Para obtener más información sobre las funciones asíncronas, [lea este artículo de medium](https://blog.bitsrc.io/understanding-asynchronous-javascript-the-event-loop-74cd408419ff).

Utilice el siguiente código para llamar a la función `mensaje` en nuestro contrato inteligente y leer el mensaje de inicio:

```javascript
// interact.js

// ...

async function main() {
  const message = await helloWorldContract.message()
  console.log("The message is: " + message)
}
main()
```

Después de ejecutar el archivo usando `npx hardhat run scripts/interact.js` en la terminal, deberíamos ver esta respuesta:

```
The message is: Hello world!
```

¡Felicidades! Acaba de leer con éxito los datos de los contratos inteligentes de la cadena de bloques de Ethereum, ¡así se hace!

### Actualizar el mensaje {#update-the-message}

En lugar de solo leer el mensaje, ¡también podemos actualizar el mensaje guardado en nuestro contrato inteligente utilizando la función `update`! Muy chido, ¿verdad?

Para actualizar el mensaje, podemos llamar directamente a la función `update` en nuestro objeto Contract instanciado:

```javascript
// interact.js

// ...

async function main() {
  const message = await helloWorldContract.message()
  console.log("The message is: " + message)

  console.log("Updating the message...")
  const tx = await helloWorldContract.update("This is the new message.")
  await tx.wait()
}
main()
```

Tenga en cuenta que en la línea 11, hacemos una llamada a `.wait()` en el objeto de transacción devuelto. Esto garantiza que nuestro script espere a que la transacción se mine en la cadena de bloques antes de salir de la función. Si la llamada `.wait()` no está incluida, es posible que el script no vea el valor `message` actualizado en el contrato.

### Lee el nuevo mensaje {#read-the-new-message}

Deberías poder repetir el [paso anterior](#read-the-init-message) para leer el valor actualizado del `mensaje`. ¡Tómese un momento y vea si puede hacer los cambios necesarios para imprimir ese nuevo valor!

Si necesitas una pista, así es como debería ser tu archivo `interact.js` en este momento:

```javascript
// interact.js

const API_KEY = process.env.API_KEY
const PRIVATE_KEY = process.env.PRIVATE_KEY
const CONTRACT_ADDRESS = process.env.CONTRACT_ADDRESS

const contract = require("../artifacts/contracts/HelloWorld.sol/HelloWorld.json")

// provider - Alchemy
const alchemyProvider = new ethers.providers.AlchemyProvider(
  (network = "goerli"),
  API_KEY
)

// signer - you
const signer = new ethers.Wallet(PRIVATE_KEY, alchemyProvider)

// contract instance
const helloWorldContract = new ethers.Contract(
  CONTRACT_ADDRESS,
  contract.abi,
  signer
)

async function main() {
  const message = await helloWorldContract.message()
  console.log("The message is: " + message)

  console.log("Updating the message...")
  const tx = await helloWorldContract.update("this is the new message")
  await tx.wait()

  const newMessage = await helloWorldContract.message()
  console.log("The new message is: " + newMessage)
}

main()
```

¡Ahora solo ejecuta el script y deberías poder ver el mensaje antiguo, el estado de la actualización y el nuevo mensaje impreso en tu terminal!

`hardhat npx ejecuta scripts/interact.js --network goerli`

```
The message is: Hello World!
Updating the message...
The new message is: This is the new message.
```

Mientras ejecuta ese script, puede notar que el paso `Actualice el mensaje...` tarda un tiempo en cargarse antes de que se cargue el nuevo mensaje. Eso se debe al proceso de minería; si tiene curiosidad sobre el seguimiento de las transacciones mientras se están minando, visite el [mempool de Alchemy](https://dashboard.alchemyapi.io/mempool) para ver el estado de una transacción. Si se elimina la transacción, también es útil comprobar [Goerli Etherscan](https://goerli.etherscan.io) y buscar el hash de su transacción.

## Parte 3: Publica tu contrato inteligente en Etherscan {#part-3-publish-your-smart-contract-to-etherscan}

Hiciste todo el trabajo duro para dar vida a tu contrato inteligente; ¡ahora es el momento de compartirlo con el mundo!

Al verificar su contrato inteligente en Etherscan, cualquiera puede ver su código fuente e interactuar con su contrato inteligente. ¡Empecemos!

### Paso 1: Genera una clave API en tu cuenta de Etherscan {#step-1-generate-an-api-key-on-your-etherscan-account}

Se necesita una clave API de Etherscan para verificar que es el propietario del contrato inteligente que está intentando publicar.

Si aún no tienes una cuenta de Etherscan, [regístrate para obtener una cuenta](https://etherscan.io/register).

Una vez que haya iniciado sesión, busque su nombre de usuario en la barra de navegación, pase el cursor sobre él y seleccione el botón **Mi perfil**.

En tu página de perfil, deberías ver una barra de navegación lateral. En la barra de navegación lateral, seleccione **Teclas API**. A continuación, presione el botón "Añadir" para crear una nueva clave de API, nombre su aplicación **hello-world**y presione el botón **Crear nueva clave de API**.

Su nueva clave de API debería aparecer en la tabla de claves de API. Copia la clave de la API en tu portapapeles.

A continuación, tenemos que añadir la clave de la API de Etherscan a nuestro archivo `.env`.

Después de añadirlo, tu archivo `.env` debería verse así:

```javascript
API_URL = "https://eth-goerli.alchemyapi.io/v2/your-api-key"
PUBLIC_KEY = "your-public-account-address"
PRIVATE_KEY = "your-private-account-address"
CONTRACT_ADDRESS = "your-contract-address"
ETHERSCAN_API_KEY = "your-etherscan-key"
```

### Contratos inteligentes desplegados por Hardhat {#hardhat-deployed-smart-contracts}

#### Instalar hardhat-etherscan {#install-hardhat-etherscan}

Publicar su contrato en Etherscan usando Hardhat es sencillo. Primero tendrás que instalar el complemento `hardhat-etherscan` para empezar. `hardhat-etherscan` verificará automáticamente el código fuente del contrato inteligente y el ABI en Etherscan. Para añadir esto, en el directorio `hello-world` se ejecuta:

```text
npm install --save-dev @nomiclabs/hardhat-etherscan
```

Una vez instalado, incluya la siguiente declaración en la parte superior de su `hardhat.config.js`, y agregue las opciones de configuración de Etherscan:

```javascript
// hardhat.config.js

require("dotenv").config()
require("@nomiclabs/hardhat-ethers")
require("@nomiclabs/hardhat-etherscan")

const { API_URL, PRIVATE_KEY, ETHERSCAN_API_KEY } = process.env

module.exports = {
  solidity: "0.7.3",
  defaultNetwork: "goerli",
  networks: {
    hardhat: {},
    goerli: {
      url: API_URL,
      accounts: [`0x${PRIVATE_KEY}`],
    },
  },
  etherscan: {
    // Your API key for Etherscan
    // Obtain one at https://etherscan.io/
    apiKey: ETHERSCAN_API_KEY,
  },
}
```

#### Verifique su contrato inteligente en Etherscan {#verify-your-smart-contract-on-etherscan}

Asegúrese de que todos los archivos estén guardados y de que todas las variables `.env` estén configuradas correctamente.

Ejecute la tarea `verificar`, pasando la dirección del contrato y la red a donde está desplegada:

```text
npx hardhat verify --network goerli DEPLOYED_CONTRACT_ADDRESS 'Hello World!'
```

Asegúrese de que `DEPLOYED_CONTRACT_ADDRESS` sea la dirección de su contrato inteligente implementado en la red de pruebas de Goerli. Además, el argumento final (`'¡Hola mundo!' `) debe ser el mismo valor de cadena utilizado [durante el paso de despliegue en la parte 1](#write-our-deploy-script).

Si todo va bien, verás el siguiente mensaje en tu terminal:

```text
Successfully submitted source code for contract
contracts/HelloWorld.sol:HelloWorld at 0xdeployed-contract-address
for verification on Etherscan. Waiting for verification result...


Successfully verified contract HelloWorld on Etherscan.
https://goerli.etherscan.io/address/<contract-address>#contracts
```

¡Felicidades! ¡Tu código de contrato inteligente está en Etherescan!

### ¡Echa un vistazo a tu contrato inteligente en Etherscan! {#check-out-your-smart-contract-on-etherscan}

Cuando navegue al enlace proporcionado en su terminal, ¡debería poder ver su código de contrato inteligente y ABI publicado en Etherscan!

**Wahooo - ¡lo hiciste campeón! ¡Ahora cualquiera puede llamar o escribir a tu contrato inteligente! ¡Estamos deseando ver lo que construyes a continuación!**

## Parte 4 - Integración de su contrato inteligente con la interfaz {#part-4-integrating-your-smart-contract-with-the-frontend}

Al final de este tutorial, sabrás cómo:

- Conecta una cartera MetaMask a tu dapp
- Leer los datos de su contrato inteligente utilizando la API [Alchemy Web3](https://docs.alchemy.com/alchemy/documentation/alchemy-web3)
- Firmar transacciones de Ethereum usando MetaMask

Para este dapp, usaremos [React](https://reactjs.org/) como nuestro marco de interfaz; sin embargo, es importante tener en cuenta que no pasaremos mucho tiempo desglosando sus fundamentos, ya que nos centraremos principalmente en llevar la funcionalidad Web3 a nuestro proyecto.

Como requisito previo, debes tener una comprensión de React a nivel principiante. Si no, recomendamos completar el tutorial oficial [Introducción a React](https://reactjs.org/tutorial/tutorial.html).

### Clone los archivos de inicio {#clone-the-starter-files}

Primero, vaya al [hello-world-part-four repositorio de GitHub](https://github.com/alchemyplatform/hello-world-part-four-tutorial) para obtener los archivos de inicio de este proyecto y clonar este repositorio en su máquina local.

Abra el repositorio clonado localmente. Tenga en cuenta que contiene dos carpetas: `archivos de inicio` y `completado`.

- `archivos de inicio`- **estaremos trabajando en este directorio**, conectaremos la interfaz de usuario a su billetera Ethereum y al contrato inteligente que publicamos en Etherscan en [Parte 3](#part-3).
- `completed`Contiene todo el tutorial completado y solo debe usarse como referencia si te quedas atascado.

A continuación, abra su copia de `starter-files` en su editor de código favorito y luego navegue hasta la carpeta `src`.

Todo el código que escribiremos permanecerá en la carpeta `src`. Editaremos el componente `HelloWorld.js` y los archivos JavaScript `util/interact.js` para dar a nuestro proyecto la funcionalidad Web3.

### Echa un vistazo a los archivos iniciales {#check-out-the-starter-files}

Antes de empezar a codificar, exploremos lo que se nos proporciona en los archivos de inicio.

#### Ejecute su proyecto de react {#get-your-react-project-running}

Comencemos por ejecutar el proyecto React en nuestro navegador. La belleza de React es que, una vez que tenemos nuestro proyecto corriendo en el navegador, cualquier cambio que guardemos será actualizado en vivo en el navegador.

Para ejecutar el proyecto, navegue hasta el directorio raíz de la carpeta `starter-files`, y ejecute `npm install` en su terminal para instalar las dependencias del proyecto:

```bash
cd starter-files
npm install
```

Una vez que hayan terminado de instalarse, ejecute `npm start` en su terminal:

```bash
npm start
```

Al hacerlo, debería abrir [http://localhost:3000/](http://localhost:3000/) en su navegador, donde verá la interfaz de nuestro proyecto. Debe consistir en un campo \(un lugar para actualizar el mensaje almacenado en su contrato inteligente\), un botón "Conectar cartera" y un botón "Actualizar".

Si intentas hacer clic en cualquiera de los botones, te darás cuenta de que no funcionan, eso es porque todavía tenemos que programar su funcionalidad.

#### El componente `HelloWorld.js` {#the-helloworld-js-component}

Volvamos a la carpeta `src` de nuestro editor y abramos el archivo `HelloWorld.js`. Es muy importante que entendamos todo en este archivo, ya que es el componente principal en React en el que trabajaremos.

En la parte superior de este archivo, notará que tenemos varias declaraciones de importación que son necesarias para que nuestro proyecto se ejecute, incluida la biblioteca React, los ganchos useEffect y useState, algunos elementos del `./util/interact.js` (¡los describiremos con más detalle pronto!), y el logotipo de Alchemy.

```javascript
// HelloWorld.js

import React from "react"
import { useEffect, useState } from "react"
import {
  helloWorldContract,
  connectWallet,
  updateMessage,
  loadCurrentMessage,
  getCurrentWalletConnected,
} from "./util/interact.js"

import alchemylogo from "./alchemylogo.svg"
```

A continuación, tenemos nuestras variables de estado que actualizaremos después de eventos específicos.

```javascript
// HelloWorld.js

//State variables
const [walletAddress, setWallet] = useState("")
const [status, setStatus] = useState("")
const [message, setMessage] = useState("No connection to the network.")
const [newMessage, setNewMessage] = useState("")
```

Esto es lo que representa cada una de las variables:

- `walletAddress`: cadena que almacena la dirección de la billetera del usuario
- `status`- una cadena que almacena un mensaje útil que guía al usuario sobre cómo interactuar con el dapp
- `mensaje` - una cadena que almacena el mensaje actual en el contrato inteligente
- `newMessage` - una cadena que almacena el nuevo mensaje que se escribirá en el contrato inteligente

Después de las variables de estado, verá cinco funciones no implementadas: `useEffect` ,`addSmartContractListener`, `addWalletListener` , `connectWalletPressed` y `onUpdatePressed`. Explicaremos lo que hacen a continuación:

```javascript
// HelloWorld.js

//called only once
useEffect(async () => {
  //TODO: implement
}, [])

function addSmartContractListener() {
  //TODO: implement
}

function addWalletListener() {
  //TODO: implement
}

const connectWalletPressed = async () => {
  //TODO: implement
}

const onUpdatePressed = async () => {
  //TODO: implement
}
```

- [`useEffect`](https://reactjs.org/docs/hooks-effect.html)- este es un gancho de React que se llama después de renderizar su componente. Debido a que tiene una matriz vacía `[]` prop pasada a ella \(ver línea 4\), solo se llamará en el _primer_ renderizado del componente. Aquí cargaremos el mensaje actual almacenado en nuestro contrato inteligente, llamaremos a nuestros oyentes de contrato inteligente y billetera, y actualizaremos nuestra interfaz de usuario para reflejar si una billetera ya está conectada.
- `addSmartContractListener`- esta función configura un oyente que vigilará el evento `UpdatedMessages` de nuestro contrato de HelloWorld y actualizará nuestra interfaz de usuario cuando se cambie el mensaje en nuestro contrato inteligente.
- `addWalletListener`- esta función configura un oyente que detecta cambios en el estado de la cartera MetaMask del usuario, como cuando el usuario desconecta su cartera o cambia de dirección.
- `connectWalletPressed`- esta función se llamará para conectar la cartera MetaMask del usuario a nuestra dapp.
- `onUpdatePressed` - esta función se llamará cuando el usuario quiera actualizar el mensaje almacenado en el contrato inteligente.

Cerca del final de este archivo, tenemos la interfaz de usuario de nuestro componente.

```javascript
// HelloWorld.js

//the UI of our component
return (
  <div id="container">
    <img id="logo" src={alchemylogo}></img>
    <button id="walletButton" onClick={connectWalletPressed}>
      {walletAddress.length > 0 ? (
        "Connected: " +
        String(walletAddress).substring(0, 6) +
        "..." +
        String(walletAddress).substring(38)
      ) : (
        <span>Connect Wallet</span>
      )}
    </button>

    <h2 style={{ paddingTop: "50px" }}>Current Message:</h2>
    <p>{message}</p>

    <h2 style={{ paddingTop: "18px" }}>New Message:</h2>

    <div>
      <input
        type="text"
        placeholder="Update the message in your smart contract."
        onChange={(e) => setNewMessage(e.target.value)}
        value={newMessage}
      />
      <p id="status">{status}</p>

      <button id="publishButton" onClick={onUpdatePressed}>
        Update
      </button>
    </div>
  </div>
)
```

Si escaneas este código con cuidado, te darás cuenta de dónde usamos nuestras diversas variables de estado en nuestra interfaz de usuario:

- En las líneas 6-12, si la billetera del usuario está conectada \(es decir, `walletAddress.length > 0`\), mostramos una versión truncada del usuario `walletAddress` en el botón con el ID "walletButton;", de lo contrario, simplemente dice "Connect Wallet".
- En la línea 17, mostramos el mensaje actual almacenado en el contrato inteligente, que se captura en la cadena `message`.
- En las líneas 23-26, utilizamos un [componente controlado](https://reactjs.org/docs/forms.html#controlled-components) para actualizar nuestra variable de estado `newMessage` cuando cambia la entrada en el campo de texto.

Además de nuestras variables de estado, también verá que las funciones `connectWalletPressed` y `onUpdatePressed` se llaman cuando se hace clic en los botones con los ID `publishButton` y `walletButton` respectivamente.

Por último, abordemos dónde se ha añadido este componente `HelloWorld.js`.

Si va al archivo `App.js`, que es el componente principal de React que actúa como contenedor para todos los demás componentes, verá que nuestro componente `HelloWorld.js` se inyecta en la línea 7.

Por último, pero no menos importante, echemos un vistazo a un archivo más proporcionado para usted, el archivo `interact.js`.

#### El archivo `interact.js` {#the-interact-js-file}

Debido a que queremos prescribir el paradigma [M-V-C](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller), querremos un archivo separado que contenga todas nuestras funciones para administrar la lógica, los datos y las reglas de nuestro dapp, y luego poder exportar esas funciones a nuestro frontend \(nuestro componente `HelloWorld.js`\).

👆🏽¡Este es el propósito exacto de nuestro archivo `interact.js`!

Vaya a la carpeta `util` en su directorio `src`, y se dará cuenta de que hemos incluido un archivo llamado `interact.js` que contendrá todas nuestras funciones y variables de interacción de contratos inteligentes y cartera.

```javascript
// interact.js

//export const helloWorldContract;

export const loadCurrentMessage = async () => {}

export const connectWallet = async () => {}

const getCurrentWalletConnected = async () => {}

export const updateMessage = async (message) => {}
```

Notará en la parte superior del archivo que hemos comentado el objeto `helloWorldContract`. Más adelante en este tutorial, dejaremos de comentar este objeto e instanciaremos nuestro contrato inteligente en esta variable, que luego exportaremos a nuestro componente `HelloWorld.js`.

Las cuatro funciones no implementadas después de nuestro objeto `helloWorldContract` hacen lo siguiente:

- `loadCurrentMessage` - esta función maneja la lógica de cargar el mensaje actual almacenado en el contrato inteligente. Hará una llamada _read_ al contrato inteligente de Hello World utilizando la [API de Alchemy Web3](https://github.com/alchemyplatform/alchemy-web3).
- `connectWallet` - esta función conectará el MetaMask del usuario a nuestra dapp.
- `getCurrentWalletConnected` - esta función comprobará si una cuenta de Ethereum ya está conectada a nuestra dapp al cargar la página y actualizará nuestra interfaz de usuario en consecuencia.
- `updateMessage` - esta función actualizará el mensaje almacenado en el contrato inteligente. Hará una llamada _write_ al contrato inteligente de Hello World, por lo que la cartera de MetaMask del usuario tendrá que firmar una transacción de Ethereum para actualizar el mensaje.

Ahora que entendemos con qué estamos trabajando, ¡vamos a averiguar cómo leer de nuestro contrato inteligente!

### Paso 3: Lea de su contrato inteligente {#step-3-read-from-your-smart-contract}

Para leer de su contrato inteligente, tendrá que configurar con éxito:

- Una conexión API a la cadena Ethereum
- Una instancia cargada de su contrato inteligente
- Una función para llamar a su función de contrato inteligente
- Un oyente para estar atento a las actualizaciones cuando cambien los datos que esté leyendo del contrato inteligente

Parecen demasiados pasos, ¡pero no te preocupes! ¡Te indicaremos como realizar cada uno de ellos paso a paso! :\)

#### Establecer una conexión API con la cadena Ethereum {#establish-an-api-connection-to-the-ethereum-chain}

Recuerdas cómo en el apartado 2 de este tutorial, utilizamos nuestra [llave de Alchemy Web3 para leer desde nuestro contrato inteligente](https://docs.alchemy.com/alchemy/tutorials/hello-world-smart-contract/interacting-with-a-smart-contract#step-1-install-web3-library)? También necesitaras una llave de Alchemy Web3 en tu dapp para leer de la cadena de bloques.

Si aún no lo tienes, instala primero [Alchemy](https://github.com/alchemyplatform/alchemy-web3) ingresando al directorio de raíz de `starter-files` y ejecutando el siguiente código en tu terminal:

```text
npm install @alch/alchemy-web3
```

[ Alchemy Web3](https://github.com/alchemyplatform/alchemy-web3) está construido sobre [Web3](https://docs.web3js.org/), de esta manera proporciona metodos mejorados de la API y otros beneficios importantes para que tu vida como desarrollador de Web3 sea mucho más fácil. Se diseñó para requerir una configuración mínima, por lo que puede comenzar a usarla en su aplicación de inmediato.

Luego, instala la librería [dotenv](https://www.npmjs.com/package/dotenv) en tu directorio de proyectos, así tendremos un lugar seguro para almancenar nuestra clave de la API una vez que lo obtengamos.

```text
npm install dotenv --save
```

Para nuestra dapp, ** utilizaremos nuestra clave para la API de Websockets ** en lugar de nuestra clave para la API HTTP, ya que nos permitirá configurar un listener que detectará cuando el mensaje guardado en el contrato inteligente cambie.

Una vez que tengas la clave de la API, crea un archivo `.env` en tu directorio de raíz y agregale tu url de Alchemy Websockets. Acontinuación, tu archivo `.env` debería verse así:

```javascript
REACT_APP_ALCHEMY_KEY = wss://eth-goerli.ws.alchemyapi.io/v2/<key>
```

¡Estamos listos para poner en marcha nuestra Alchemy Web3 endpoint en nuestra dapp! Volvamos de nuevo a nuestro `interact.js`, que se encuentra anidada dentro de nuestro archivo `util` y agrega el siguiente código al comienzo del archivo:

```javascript
// interact.js

require("dotenv").config()
const alchemyKey = process.env.REACT_APP_ALCHEMY_KEY
const { createAlchemyWeb3 } = require("@alch/alchemy-web3")
const web3 = createAlchemyWeb3(alchemyKey)

//export const helloWorldContract;
```

Primero importamos la clave Alchemy de nuestro archivo `.env` y luego pasamos nuestro `alchemyKey` a `createAlchemyWeb3` para establecer nuestra Alchemy Web3 endpoint.

Una vez listo el endpoint, ¡es momento de cargar nuestro Contrato inteligente!

#### Carga de tu Contrato inteligente Hello World {#loading-your-hello-world-smart-contract}

Para cargar tu Contrato inteligente Hello World, necesitaras la dirección del contrato y ABI, puedes encontrar ambos en Etherscan si has completado previamente [ la parte 3 de este tutorial.](/developers/tutorials/hello-world-smart-contract-fullstack/#part-3-publish-your-smart-contract-to-etherscan-part-3-publish-your-smart-contract-to-etherscan)

#### Cómo obtener tu contrato ABI de Etherscan {#how-to-get-your-contract-abi-from-etherscan}

Si omitiste la parte 3 de este tutorial, puedes utilizar el contrato Hello World con dirección [0x6f3f635A9762B47954229Ea479b4541eAF402A6A](https://goerli.etherscan.io/address/0x6f3f635a9762b47954229ea479b4541eaf402a6a#code). Puedes encontrar su ABI [aquí](https://goerli.etherscan.io/address/0x6f3f635a9762b47954229ea479b4541eaf402a6a#code).

Se necesita un contrato ABI para especificar qué función utilizará un contrato y para asegurar que la función devolverá datos en el formato esperado. Una vez que hayamos copiado nuestro contrato ABI, lo guardaremos como un archivo JSON que se llamará `contract-abi.json` en tu directorio `src`.

Tu contrato -abi.json deberá ser guardado en tu carpeta src.

Con la dirección de nuestro contrato ABI y AlchemyWeb3 endpoint, podemos utilizar el [contract method](https://docs.web3js.org/api/web3-eth-contract/class/Contract) para cargar una instancia de nuestro Contrato inteligente. Importa tu contrato ABI dentro del archivo `interact.js` y agrega la direción de tu contratro.

```javascript
// interact.js

const contractABI = require("../contract-abi.json")
const contractAddress = "0x6f3f635A9762B47954229Ea479b4541eAF402A6A"
```

Ahora podemos suprimir comentario de nuestra variable `helloWorldContract`, y cargar el contrato inteligente utilizando nuestro AlchemyWeb3 endpoint:

```javascript
// interact.js
export const helloWorldContract = new web3.eth.Contract(
  contractABI,
  contractAddress
)
```

A modo de repaso, las primeras 12 lineas de tu `interact.js` ahora deberían verse así:

```javascript
// interact.js

require("dotenv").config()
const alchemyKey = process.env.REACT_APP_ALCHEMY_KEY
const { createAlchemyWeb3 } = require("@alch/alchemy-web3")
const web3 = createAlchemyWeb3(alchemyKey)

const contractABI = require("../contract-abi.json")
const contractAddress = "0x6f3f635A9762B47954229Ea479b4541eAF402A6A"

export const helloWorldContract = new web3.eth.Contract(
  contractABI,
  contractAddress
)
```

Ahora que tenemos cargado nuestro contrato, ¡podemos implementar nuestra función `loadCurrentMessage`!

#### Implementación de `loadCurrentMessage` en tu archivo `interact.js` {#implementing-loadCurrentMessage-in-your-interact-js-file}

Esta función es muy sencilla. Haremos una simple llamada async Web3 para así leer de nuestro contrato. Nuestra función devolverá el mensaje almacenado en el contrato inteligente:

Actualiza el `loadCurrentMessage` en tu archivo `interact.js` de la siguiente manera:

```javascript
// interact.js

export const loadCurrentMessage = async () => {
  const message = await helloWorldContract.methods.message().call()
  return message
}
```

Dado que queremos mostrar este contrato inteligente en nuestra UI, actualizemos la función `useEffect` en nuestro componente `HelloWorld.js` de la siguiente manera:

```javascript
// HelloWorld.js

//called only once
useEffect(async () => {
  const message = await loadCurrentMessage()
  setMessage(message)
}, [])
```

Recuerda, solo queremos que nuestro `loadCurrentMessage` sea llamado una sola vez durante la primera renderización del componente. Pronto implementaremos `addSmartContractListener` para que actualice automáticamente la UI cada vez que cambie el mensaje en el contrato inteligente.

Antes de profundizar sobre nuestro listener, ¡revisemos lo que tenemos hasta ahora! Guarda tus archivos `HelloWorld.js` y `interact.js` y luego ve a [http://localhost:3000/](http://localhost:3000/)

Notaras que el mensaje actual ya no dice "Sin conexión a la red". En su lugar reflejará el mensaje almacenado en el contrato inteligente. ¡Fantástico!

#### Ahora, tu UI debería reflejar el mensaje almacenado en el contrato inteligente {#your-UI-should-now-reflect-the-message-stored-in-the-smart-contract}

Hablando del listener...

#### Implementa `addSmartContractListener` {#implement-addsmartcontractlistener}

Si haces memoria, en el archivo `HelloWorld.sol` que redactamos en [ la parte 1 de esta serie de tutoriales](https://docs.alchemy.com/alchemy/tutorials/hello-world-smart-contract#step-10-write-our-contract), recordaras que existe un evento de contrato inteligente que se llama `UpdatedMessages` y que se emite una vez que la función `update` es utilizada \(ver lineas 9 y 27\):

```javascript
// HelloWorld.sol

// Specifies the version of Solidity, using semantic versioning.
// Learn more: https://solidity.readthedocs.io/en/v0.5.10/layout-of-source-files.html#pragma
pragma solidity ^0.7.3;

// Defines a contract named `HelloWorld`.
// Un contrato es una colección de funciones y datos (su estado). Una vez desplegado, un contrato reside en una dirección específica en la blockchain de Ethereum. Learn more: https://solidity.readthedocs.io/en/v0.5.10/structure-of-a-contract.html
contract HelloWorld {

   //Emitted when update function is called
   //Smart contract events are a way for your contract to communicate that something happened on the blockchain to your app front-end, which can be 'listening' for certain events and take action when they happen.
   event UpdatedMessages(string oldStr, string newStr);

   // Declares a state variable `message` of type `string`.
   // Las variables de estado son variables cuyos valores se almacenan permanentemente en el almacenamiento del contrato. The keyword `public` makes variables accessible from outside a contract and creates a function that other contracts or clients can call to access the value.
   string public message;

   // Similar to many class-based object-oriented languages, a constructor is a special function that is only executed upon contract creation.
   // Los constructores se utilizan para inicializar los datos del contrato. Learn more:https://solidity.readthedocs.io/en/v0.5.10/contracts.html#constructors
   constructor(string memory initMessage) {

      // Accepts a string argument `initMessage` and sets the value into the contract's `message` storage variable).
      message = initMessage;
   }

   // A public function that accepts a string argument and updates the `message` storage variable.
   function update(string memory newMessage) public {
      string memory oldMsg = message;
      message = newMessage;
      emit UpdatedMessages(oldMsg, newMessage);
   }
}
```

Los eventos del contrato inteligente son una forma de comunicar que ha pasado algo \(es decir, hubo un _event_\) en la blockchain que repercutió en tu aplicación front-end. Podría tratarse de un 'listening' para eventos específicos y , al mismo tiempo, sobre las medidas de acción a implementar cuando ocurren.

La función `addSmartContractListener` prestará atención a nuestro evento de contrato inteligente Hello World `UpdatedMessages`, y actualizará nuestro UI para que muestre el nuevo mensaje.

Modifica `addSmartContractListener` de la siguiente manera:

```javascript
// HelloWorld.js

function addSmartContractListener() {
  helloWorldContract.events.UpdatedMessages({}, (error, data) => {
    if (error) {
      setStatus("😥 " + error.message)
    } else {
      setMessage(data.returnValues[1])
      setNewMessage("")
      setStatus("🎉 Your message has been updated!")
    }
  })
}
```

Desmenucemos lo que pasa cuando el listener detecta un evento:

- Si ocurre un error al momento de emitir el evento, este se verá reflejado en la UI a través de nuestra variable de estado `status`.
- Caso contrario, utilizaremos el objeto devuelto `data`. `data.returnValues` es un arreglo indexado a cero donde el primer elemento almancena el mensaje anterior, mientras que el segundo elemento almacena el mensaje actualizado. En conjunto, en un evento eficaz colocaremos nuestro string `message` al mensaje actualizado, resetearemos el string `newMessage`, y actualizaremos nuestra variable de estado `status` para que refleje que un nuevo mensaje se ha publicado en nuestro contrato inteligente.

Por último, ejecutemos nuestro listener en nuestra función `useEffect` para que se inicialize en la primera devolución del componente `HelloWorld.js`. Entonces, tu función `useEffect` debería verse así:

```javascript
// HelloWorld.js

useEffect(async () => {
  const message = await loadCurrentMessage()
  setMessage(message)
  addSmartContractListener()
}, [])
```

Ahora que podemos leer de nuestro contrato inteligente, también ¡sería genial descifrar cómo podemos escribirlo! Sin embargo, para escribir en nuestra dapp, primero debemos tener una wallet de Ethereum conectada a la misma.

Por lo tanto, la próxima cuestión que abordaremos será crear nuestra wallet de Ethereum \(MetaMask\) para luego conectarla con nuestra dapp.

### Paso 4: Crea tu wallet de Ethereum {#step-4-set-up-your-ethereum-wallet}

Para poder escribir en la chain de Ethereum, los usuarios deben firmar las transacciones utilizando la clave privada de su wallet virtual. Para este tutorial, utilizaremos [MetaMask](https://metamask.io/), una billetera virtual en el browser que se utiliza para administrar la dirección de tu cuenta de Ethereum, ya que hace que la firma de esta transacción sea bastante simple para el usuario final.

Si quiere más información sobre cómo funcionan las transacciones en Ethereum, eche un vistazo a [esta página](/developers/docs/transactions/) de Ethereum Foundation.

#### Descargar MetaMask {#download-metamask}

Puede descargar y crear una cuenta Metamask gratis [aquí](https://metamask.io/download.html). Cuando estes creando una cuenta, o si ya tienes una, asegurate de cambiar de “Goerli Test Network” en la parte superior \(para que no se trate de dinero real\).

#### Añada ether a partir de un grifo {#add-ether-from-a-faucet}

Necesitaremos algunos Eth falsos para poder firmar una transacción en la blockchain de Ethereum. Para obtener Eth puedes ir a [FaucETH](https://fauceth.komputing.org) e ingresar la direcciíon de tu cuenta Goerli, haz click en “Request funds”, luego selecciona “Ethereum Testnet Goerli” y por último, haz click en el botón “Request funds” nuevamente. Debería ver el Eth en su cuenta de MetaMask poco después.

#### Revisa tu saldo {#check-your-balance}

Para verificar que nuestro saldo esté ahí, realicemos una solicitud [eth_getBalance](https://docs.alchemyapi.io/alchemy/documentation/alchemy-api-reference/json-rpc#eth_getbalance) usando la [herramienta de compositor de Alchemy](https://composer.alchemyapi.io/?composer_state=%7B%22network%22%3A0%2C%22methodName%22%3A%22eth_getBalance%22%2C%22paramValues%22%3A%5B%22%22%2C%22latest%22%5D%7D). Esto devolverá la cantidad de Eth en nuestra billetera. Después de introducir la dirección de su cuenta de Metamask y hacer clic en «Send Request» (Enviar Solicitud), debería ver una respuesta como esta:

```text
{"jsonrpc": "2.0", "id": 0, "result": "0xde0b6b3a7640000"}
```

**NOTA:** Este resultado esta en wei, no en eth. Wei se usa como la denominación más pequeña de Ether. La conversión de wei a eth es: 1 eth = 10¹⁸ wei. Entonces si convertimos 0xde0b6b3a7640000 a decimal, obtenemos 1\*10¹⁸, que equivale a 1 eth.

¡Fiu! Nuestro dinero de prueba está ahí sin problemas. 🤑

### Paso 5: Conectate a tu UI en MetaMask {#step-5-connect-metamask-to-your-UI}

Ahora que nuestra billetera de MetaMask está configurada, vamos a conectar nuestra dapp a ella.

#### Función `connectWallet` {#the-connectWallet-function}

En nuestro archivo `interact.js`, implementaremos la función `connectWallet`, el cual podremos ejecutar en nuestro componente `HelloWorld.js` posteriormente.

Procedamos a modificar `connectWallet` como se muestra a continuación:

```javascript
// interact.js

export const connectWallet = async () => {
  if (window.ethereum) {
    try {
      const addressArray = await window.ethereum.request({
        method: "eth_requestAccounts",
      })
      const obj = {
        status: "👆🏽 Write a message in the text-field above.",
        address: addressArray[0],
      }
      return obj
    } catch (err) {
      return {
        address: "",
        status: "😥 " + err.message,
      }
    }
  } else {
    return {
      address: "",
      status: (
        <span>
          <p>
            {" "}
            🦊 <a target="_blank" href={`https://metamask.io/download.html`}>
              You must install MetaMask, a virtual Ethereum wallet, in your
              browser.
            </a>
          </p>
        </span>
      ),
    }
  }
}
```

Entonces ¿Qué hace exactamente este extenso código?

Bueno, en primer lugar, verifica que `window.ethereum` este habilitado en tu navegador.

`window.ethereum` es una API global inyectada por MetaMask y otros proveedores de billeteras que permite a los sitios web solicitar las cuentas de Ethereum de los usuarios. Si es aprobado, puede leer información de la blockchain a la que está conectado el usuario y sugerir que este firme mensajes y transacciones. Revise la [documentación de MetaMask](https://docs.metamask.io/guide/ethereum-provider.html#table-of-contents) para obtener más información.

Si `window.ethereum` _no está_ presente, eso significa que MetaMask no está instalado. Esto resulta en la devolución de un objeto JSON, donde el `address` devuelto es una cadena vacía y el objeto JSX `status` muestra que el usuario debe instalar MetaMask.

Ahora, si `window.ethereum` _está_ presente, las cosas se ponen interesantes.

Al utilizar un try/catch loop, intentaremos conectarnos a MetaMask ejecutando

window.ethereum.request({ method: "eth_requestAccounts" });. La invocación de esta función abrirá MetaMask en el navegador, donde se le solicitará al usuario conectar su billetera a su dapp.

- Si el usuario decide conectarse, `method: "eth_requestAccounts"` devolverá un arreglo que contiene todas las direcciones de cuenta del usuario que se conectaron a la dapp. De igual manera, nuestra función `connectWallet` devolverá un objeto JSON que contine la _primera_ `address` de este arreglo \(ver la línea 9\) y un mensaje de `status` que solicita al usuario escribir un mensaje al contrato inteligente.
- Si el usuario rechaza la conexión, el objeto JSON tendrá una cadena vacía para la `address` devuelta y un mensaje de `status` donde se refleje que el usuario rechazó la conexión.

Una vez escrita la función `connectWallet`, el siguiente paso es ejecutarlo en nuestro componente `HelloWorld.js`.



#### Agrega la función `connectWallet` a tu componente de UI `HelloWorld.js` {#add-the-connectWallet-function-to-your-HelloWorld-js-ui-component}

Dirígitete a la función `onnectWalletPressed` en `HelloWorld.js`, y actualizalo de la siguiente manera:



```javascript
// HelloWorld.js

const connectWalletPressed = async () => {
  const walletResponse = await connectWallet()
  setStatus(walletResponse.status)
  setWallet(walletResponse.address)
}
```


¿Notas como gran parte de nuestra funcionalidad se abstrae de nuestro componente `HelloWorld.js` del archivo `interact.js`? ¡Esto es así para cumplir con el paradigma M-V-C!

En `connectWalletPressed`, simplemente hacemos una llamada en espera a nuestra función `conectWallet` importada y, utilizando su respuesta, actualizamos nuestras variables `status` y `walletAddress` a través de sus hooks de estado.

Guardemos ambos archivos \(`HelloWorld.js` and `interact.js`\) y probemos nuestra UI.

Abre tu navegador con el link [http://localhost:3000/](http://localhost:3000/), y presiona el botón "Conectar Wallet" en el margen superior derecho de la página.

Si tiene MetaMask instalado, se le debería solicitar conectar su billetera a su dapp. Acepte la invitación para establecer la conexión.

Deberías poder visualizar que, ahora, el botón de wallet muestra que tu dirección está conectada.  ¡Fantástico!

A continuación, pruebe actualizar la página... esto es extraño. Nuestro botón de billetera nos está solicitando conectar MetaMask, aunque ya está conectado...

¡No tengas miedo! Podemos solucionarlo facilmente, implementando `getCurrentWalletConnected` (¿entiendes?). Esto verificará si ya existe una dirección conectada a nuestra dapp y, en consecuencia, actualizará nuestra UI.



#### Función `getCurrentWalletConnected` {#the-getcurrentwalletconnected-function}

Actualiza tu función `getCurrentWalletConnected` en el archivo `interact.js` como se muestra a continuación:



```javascript
// interact.js

export const getCurrentWalletConnected = async () => {
  if (window.ethereum) {
    try {
      const addressArray = await window.ethereum.request({
        method: "eth_accounts",
      })
      if (addressArray.length > 0) {
        return {
          address: addressArray[0],
          status: "👆🏽 Write a message in the text-field above.",
        }
      } else {
        return {
          address: "",
          status: "🦊 Connect to MetaMask using the top right button.",
        }
      }
    } catch (err) {
      return {
        address: "",
        status: "😥 " + err.message,
      }
    }
  } else {
    return {
      address: "",
      status: (
        <span>
          <p>
            {" "}
            🦊 <a target="_blank" href={`https://metamask.io/download.html`}>
              You must install MetaMask, a virtual Ethereum wallet, in your
              browser.
            </a>
          </p>
        </span>
      ),
    }
  }
}
```


Este código es _bastante_ similar a la función `connectWallet` que escribimos en el paso anterior.

La principal diferencia es que, en vez de llamar al método `eth_requestAccount`, que abre MetaMask para que el usuario conecte su billetera, aquí llamamos al método `eth_accounts`, que simplemente devuelve un arreglo que contiene las direcciones de MetaMask que se encuentran conectadas a nuestra dapp.

Para ver esta función, lo podemos ejecutar en nuestra función `useEffect` de nuestro componente `HelloWorld.js`:



```javascript
// HelloWorld.js

useEffect(async () => {
  const message = await loadCurrentMessage()
  setMessage(message)
  addSmartContractListener()

  const { address, status } = await getCurrentWalletConnected()
  setWallet(address)
  setStatus(status)
}, [])
```


Note que usamos la respuesta de nuestra llamada a `getCurrentWalletConnected` para actualizar nuestras variables de estado `walletAddress` y `status`.

Una vez que agregues este código, refrescaremos la ventana de nuestro navegador.

¡Magnífico! El botón debería decir que está conectado y mostrar una vista previa de la dirección de su billetera conectada, incluso después de actualizar la página.



#### Implementa `addWalletListener` {#implement-addwalletlistener}

El último paso en la configuración de la billetera de dapp es implementar el oyente de billetera para que nuestra interfaz se actualice cuando el estado de la billetera cambie, por ejemplo, cuando el usuario se desconecte o cambie de cuenta.

En tu archivo `HelloWorld.js`, modifica tu función `addWalletListener` como se ve a continuación:



```javascript
// HelloWorld.js

function addWalletListener() {
  if (window.ethereum) {
    window.ethereum.on("accountsChanged", (accounts) => {
      if (accounts.length > 0) {
        setWallet(accounts[0])
        setStatus("👆🏽 Write a message in the text-field above.")
      } else {
        setWallet("")
        setStatus("🦊 Connect to MetaMask using the top right button.")
      }
    })
  } else {
    setStatus(
      <p>
        {" "}
        🦊 <a target="_blank" href={`https://metamask.io/download.html`}>
          You must install MetaMask, a virtual Ethereum wallet, in your browser.
        </a>
      </p>
    )
  }
}
```


En este punto, apuesto a que ni siquiera necesitas de nuestra ayuda para entender lo que ocurre. Pero, para ser más exhaustivos, desglosémoslo rápidamente:

- Primero, nuestra función verifica si `window.ethereum` está habilitado \(esto es si MetaMask está instalado\). 
    - Si no lo está, simplemente establecemos nuestra variable de estado `status` a una cadena JSX que solicite al usuario instalar MetaMask.
  - Si está habilitado, configuramos el oyente `window.ethereum.on("accountsChanged")` en la línea 3, que escucha cambios de estado en la billetera de MetaMask, incluyendo cuando el usuario conecte una cuenta adicional a la dapp, cambie de cuenta o desconecte una cuenta. Si hay por lo menos una cuenta conectada, la variable de estado `walletAddress` es actualizada como la primera cuenta en el arreglo `accounts` devuelto por el oyente. De lo contrario, `walletAddress` se establece como cadena vacía.

Por último, debemos ejecutarlo en nuestra función `useEffect`:



```javascript
// HelloWorld.js

useEffect(async () => {
  const message = await loadCurrentMessage()
  setMessage(message)
  addSmartContractListener()

  const { address, status } = await getCurrentWalletConnected()
  setWallet(address)
  setStatus(status)

  addWalletListener()
}, [])
```


¡Y eso es todo! Hemos terminado de programar toda la funcionalidad de nuestra wallet exitosamente! Nuestra última tarea es actualizar el mensaje almacenado en nuestro contrato inteligente.



### Paso 6: Implementa la función `updateMessage` {#step-6-implement-the-updateMessage-function}

¡Hemos legado final! En el `updateMessage` de tu archivo `interact.js`, haremos lo siguiente:

1. Asegurate de que el mensaje que deseas publicar en nuestro contrato inteligente sea válido
2. Firmamos la transacción utilizando MetaMask
3. Ejecuta esta función desde nuestro componente frontend `HelloWorld.js`

No nos llevará mucho ¡terminemos esta dapp!



#### Manejo de errores de entrada {#input-error-handling}

Tiene sentido tener algún chequeo de error en el input al comienzo de la función.

Queremos que nuestra función retorne rápido si, no existe una extensión de MetaMask instalada, no existe una wallet conectada \(i.e. the `address` es un string vacío\), o `message` también es un string vacío. Agreguemos el siguiente chequeo de error a `updateMessage`:



```javascript
// interact.js

export const updateMessage = async (address, message) => {
  if (!window.ethereum || address === null) {
    return {
      status:
        "💡 Connect your MetaMask wallet to update the message on the blockchain.",
    }
  }

  if (message.trim() === "") {
    return {
      status: "❌ Your message cannot be an empty string.",
    }
  }
}
```


Ahora que cuenta con un chegueo de error adecuado ¡ha llegado el momento de firmar la transacción con MetaMask!



#### Firma de nuestra transacción {#signing-our-transaction}

Si ya estás cómodo con las transacciones tradicionales web3 de Ethereum, el código que escribiremos a continuación te resultará bastante familiar. Debajo de tu código de chequeo de error, agrega lo siguiente a `pdateMessage`:



```javascript
// interact.js

//set up transaction parameters
const transactionParameters = {
  to: contractAddress, // Required except during contract publications.
  from: address, // must match user's active address.
  data: helloWorldContract.methods.update(message).encodeABI(),
}

//sign the transaction
try {
  const txHash = await window.ethereum.request({
    method: "eth_sendTransaction",
    params: [transactionParameters],
  })
  return {
    status: (
      <span>
        ✅{" "}
        <a target="_blank" href={`https://goerli.etherscan.io/tx/${txHash}`}>
          View the status of your transaction on Etherscan!
        </a>
        <br />
        ℹ️ Once the transaction is verified by the network, the message will be
        updated automatically.
      </span>
    ),
  }
} catch (error) {
  return {
    status: "😥 " + error.message,
  }
}
```


Veamos qué es lo que ocurre.  Primero, establezcamos los parámetros para nuestra transacción:

- `to` especifica la dirección del receptor \(nuestro contrato inteligente\)
- `from` muestra quién firma la transacción, la variable `address` muestra lo que pasamos a la función
- `data` contiene la ejecución del método `update` de nuestro contrato inteligente Hello World, que recibe la variable de tipo cadena `message` como una entrada

Luego, realizamos una ejecución en espera, `window.ethereum.request`, donde le pedimos a MetaMask que firme la transacción. Ten en cuenta que, en las lineas 11 y 12, especificamos nuestro método ETH `eth_sendTransaction` y le pasamos `transactionParameters`.

En este punto, MetaMask se abrirá en el navegador y solicitará al usuario firmar o rechazar la transacción.

- Si la transacción tiene éxito, la función devolverá un objeto de JSON donde el string JSX `status` le pedirá al usuario que revise Etherscan para obtener más información sobre su transacción.
- Si la transacción falla, la función devolverá un objeto JSON donde el string `status` transmitirá el mensaje de error.

Entonces, nuestra función `updateMessage` debería verse así:



```javascript
// interact.js

export const updateMessage = async (address, message) => {
  //input error handling
  if (!window.ethereum || address === null) {
    return {
      status:
        "💡 Connect your MetaMask wallet to update the message on the blockchain.",
    }
  }

  if (message.trim() === "") {
    return {
      status: "❌ Your message cannot be an empty string.",
    }
  }

  //set up transaction parameters
  const transactionParameters = {
    to: contractAddress, // Required except during contract publications.
    from: address, // must match user's active address.
    data: helloWorldContract.methods.update(message).encodeABI(),
  }

  //sign the transaction
  try {
    const txHash = await window.ethereum.request({
      method: "eth_sendTransaction",
      params: [transactionParameters],
    })
    return {
      status: (
        <span>
          ✅{" "}
          <a target="_blank" href={`https://goerli.etherscan.io/tx/${txHash}`}>
            View the status of your transaction on Etherscan!
          </a>
          <br />
          ℹ️ Once the transaction is verified by the network, the message will
          be updated automatically.
        </span>
      ),
    }
  } catch (error) {
    return {
      status: "😥 " + error.message,
    }
  }
}
```


Por último, necesitamos conectar nuestra función `updateMessage` a nuestro componente `HelloWorld.js`.



#### Conecta `updateMessage` al frontend `HelloWorld.js` {#connect-updatemessage-to-the-helloworld-js-frontend}

Nuestra función `onUpdatePressed` deberá realizar una ejecución en espera a la función importada `updateMessage` y modificar la variable de estado `status` para que muestre si funcionó o falló nuestra transacción:



```javascript
// HelloWorld.js

const onUpdatePressed = async () => {
  const { status } = await updateMessage(walletAddress, newMessage)
  setStatus(status)
}
```


Es simple y claro. Y adivina qué... ¡TU DAPP ESTÁ TERMINADA!

¡Adelante, ve a probar el botón **Update**!



### Crea tu propia dapp personalizada {#make-your-own-custom-dapp}

¡Bravo! ¡Has llegado al final del tutorial! A modo de repaso, esto es lo que aprendiste:

- como conectar una wallet de MetaMask a tu proyecto dapp
- Leer los datos de su contrato inteligente utilizando la API [Alchemy Web3](https://docs.alchemy.com/alchemy/documentation/alchemy-web3)
- Firmar transacciones de Ethereum usando MetaMask

Ya estás preparado para aplicar los conocimientos que adquiriste en este tutorial, y así, crear tu propio proyecto dapp personalizado! De más está decir que, ante cualquier duda, puedes pedirnos ayuda en [AlchemyDiscord](https://discord.gg/gWuC7zB). 🧙‍♂️

Cuando finalice este tutorial, cuéntenos cómo fue su experiencia o comparta algún comentario etiquetándonos en Twitter [@alchemyplatform](https://twitter.com/AlchemyPlatform).
