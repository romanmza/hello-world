# Readme
# Introducción
El presente documento describe el funcionamiento de la nueva librería CI-Scrapers, así como los pasos necesarios para poder utilizarla y agregar nuevos competidores a la misma.
CI-Scrapers es una librería desarrollada en Typescript, habiendo tomado como base para el funcionamiento, la librería anterior: Competencia-Scrappers. El objetivo de esta mejora fue implementar una sistema estratificado, en donde se separen las etapas de obtención de información, procesamiento y validación de datos, para poder facilitar la escalabilidad y el control de errores. Por otro lado también se optó por el uso de Typescript puesto que este lenguaje posee características que permiten minimizar los errores y escribir código más limpio y más sencillo para realizar pruebas.

# Tabla de Contenido

# Estructura y Funcionamiento de la Librería
CI-Scrapers se diseñó teniendo en cuenta los siguientes principios:
- Estratificación
- Simplicidad
- Escalabilidad

## Estratificación
El objetivo es poder separar las etapas que competen a todo el proceso de scraping, en distintos subprocesos. De esta forma se logra una mayor escalabilidad, una detección de errores más eficiente y un proceso más simple para incorporar nuevos competidores.

Para ello, se cuentan con tres etapas o subprocesos:
- Crawling
- Parsing
- Scraping

(aquí va el gráfico de estas etapas)


1. Crawling
Incluye todo lo referido a la obtención de la información cruda (o sin procesar) del competidor. Aquí se encuentran todos los procesos como selección de proxies, configuración de headers, request HTLM/API, reintentos, gestión de errores de conexión/timeout, entre otros
2. Parsing
Hace referencia a todos los procesos que se llevan a cabo en cada competidor para procesar y filtrar la información obtenida, en función de lo que se requiera para cada plugin. Aquí es donde se desarrolla la mayor tarea al momento de integrar un competidor, ya que cada rival utiliza una metodología en particular para presentar su información y se requiere un análisis en profundidad para uan correcta extracción de la información deseada. 
3. Scraping
Hace referencia al proceso de salida, según los requerimientos de los clientes que utilizarán esta librería, para cada plugin. Incluye todo el proceso de validación según el tipo de dato y lo que se espera a la salida vs. lo que se obtuvo en el proceso de parsing. 

## Simplicidad

## Escalabilidad

# Sistema de Versionado
CI-Scrapers está diseñada para responder al sistema de Release Process implementado en los proyectos de MELI. El mismo asegura que todas las etapas desde la generación del código fuente hasta su puesta en marcha en amiente productivo, pasen por un proceso de verificación de sintaxis, errores, pruebas internas y compatibilidad con versiones anteriores. 
## Ventajas de Release Process:
- Rapidez. El flujo basado en eventos del backend, nos permite optimizar al máximo los tiempos de ejecución de cada tarea dentro del proceso.

- Robustez. Una falla en cualquier control de calidad interrumpe el flujo e indica claramente cual fue el error y que se necesita hacer para avanzar al próximo paso.

- Infraestructura Unificada. Tanto el Integrador Continuo como el Build Server comparten infraestructura y se comportan de la misma manera.

- Soporte dedicado. Cualquier problema con Release Process va a ser atentido directamente por el equipo especialista, apuntando a solucionar los problemas rápidamente y con la menor cantidad de interacciones.

- Ejecución de tests. Se ejecutan todos los tests de la aplicación cuando es necesario. Es decir, que si para un commit ya fueron ejecutados, no se hará nuevamente.

- Chequeos de cobertura de código. Se controlan tanto la cobertura de cada pull request como de la aplicación entera.

- Chequeos de seguridad. Se controla que no existan credenciales hardcodeadas en nuestro código.

- Versiones automáticas. Podemos automatizar el creado de las versiones de nuestras apps.

- Chequeos de calidad de código. Se realizan chequeos sobre el código para detectar problemas de eficiencia, mantenibilidad, legibilidad, etc


Para implementar Release Process, se requiere que el sistema de versionado también sea coherente.
## Estrcutura del versionado
La nomenclatura de la versión se compone de tres números separados por puntos, por ejemplo:

`2.4.13`

Cada uno de estos tres números se debe incrementar en forma secuencial (formato N°N°N°) con cada actualización de versión, 
- El primer número de la izquierda hace referencia a la versión principal de la librería, y se incrementa sólo ante cambios profundos en el funcionamiento en general del proyecto.
- El segundo número se modifica al agregar a la librería un nuevo feature: puede ser un nuevo competidor, o un nuevo plugin que utilicen todos los competidores. 
- El tercer número (de la derecha) se incrementa cuando se realizan ajustes/correcciones sobre los plugins/competidores ya integrados

# Plugins
Los plugins son los componentes fundamentales de la librería, encargados de obtener y procesar cierta información en función de los requerimientos.

Se disponen de seis plugins fundamentales:

- IdFromURL
- Search
- VIP
- Sellers
- Offers
- Shipping

## IdFromURL
Este plugin se encarga exclusivamente de obtener el ID de producto según la URL suministrada por el usuario.
### Parámetros específicos de Entrada:
`urlString`: URL del competidor que lleva a un producto específico del mismo.
### Parámetros específicos de Salida:
`itemId`: ID del producto encontrado en la URL proporcionada.

## Search
Este plugin se utiliza para obtener los primeros 20 IDs de resultados según un criterio de búsqueda en el competidor especificado. Por ejemplo: obtener los ID de los primeros 20 resultados de la búsqueda "celular" en el competidor "EXI".  
El plugin devolverá los 20 primeros IDs (si existen), o la cantidad total de coincidencias, si hubesen menos de 20.

### Parámetros específicos de Entrada:
`searchString`: Corresponde a una cadena de texto con el criterio de búsqueda, por ejemplo "televisores hitachi"

### Parámetros específicos de Salida:
`itemIds`: Lista de IDs de productos encontrados

##VIP (Vista Interna de Producto)
Este plugin se utiliza para obtener la información detallada de un producto específico. Para ello se debe conocer el ID de dicho producto para ingresarlo como parámetro de entrada.
### Parámetros específicos de Entrada:
`itemId`: ID del producto que se requiere la información.
### Parámetros específicos de Salida:
`currency`: Código de la moneda del país de donde pertenezca el competidor (Ejemplo: CLP para Peso Chileno).

`image`: Link desde donde se puede descargar la imagen de producto, suministrada por el competidor.

`price`: Precio del producto.

`title`: Título del producto.

`url`: Link directo a la publicación del competidor donde se encuentra el producto.

`features`: Corresponde a la descripción de producto que coloca el competidor. Esta información no está siempre disponible en todas las publicaciones.

`extra_data`: Conjunto de datos (pares nombre, valor) con información adicional del producto. Esta información no está siempre disponible en todas las publicaciones.

## Sellers
Se utiliza para obtener de un competidor y un ítem específico, la lista de vendedores de ese ítem.

### Parámetros específicos de Entrada:
`itemId`: ID del producto del que se requiere la información.

### Parámetros específicos de Salida:
`id`: ID del competidor. Se compone de el código del mismo (ej: "EXI" para Éxito) + ID de producto.

`name`: Nombre o título del producto.

`permalink`: Link directo a la publicación del competidor donde se encuentra el producto.

`sellers`: Es un objeto que contendrá los vendedores para ese producto, y las características de cada uno, con el formato [seller1], [seller2], ...,[sellerN]. Cada seller contendrá:

   1. `id`: ID del vendedor.
   2. `name`: nombre del vendedor.
   3. `permalink`: url dentro del competidor.  
   4. `first_party`: es una bandera, indicará true si el vendedor coincide con el competidor analizado.
   5. `fulfillment`: es una bandera que indica si el envío es gratis.
   6. `seller_winner`: es una bandera que indica si este vendedor es el vendedor principal del producto.
   7. `reputation`: Es un valor que indica la reputación que posee este vendedor.
   8. `active_publications`: Es un valor que indica la cantidad de publicaciones activas que tiene este vendedor en este competidor. 

## Offers
Este plugin se utiliza, dado un competidor, un ID de un vendedor válido dentro del mismo, y un ID de producto, para obtener una lista con las distintas formas de venta y de pago que ofrece ese vendedor para ese producto determinado, se puede obtener información sobre los medios de pago admitidos, cantidad de cuotas, tasa de interés, entre otros.

### Parámetros específicos de Entrada:
`itemId`: ID del producto del que se requiere la información.

`sellerId`: ID del vendedor que ofrece ese producto.

### Parámetros específicos de Salida:
`offers`:Es un objeto que contendrá las distintas opciones de pago para ese producto, y las características de cada uno, con el formato [offer1], [offer2], ...,[offerN]. Para las opciones de pago que ofrecen alternativas en cuotas, se considera la primera (pago en 1 cuota) y la última (pago en el mayor número de cuotas, por ejemplo 48). Cada offer contendrá:
   1. `cashback`: Indica un monto en caso de que exista devolución de dinero en la compra.
   3. `combo`: Indica si el método de pago es con interés ("L"), sin interés ("J"), o Boleto ("B", se utiliza en Brasil).
   4. `currency_id`: Código de la moneda del país de donde pertenezca el competidor (Ejemplo: CLP para Peso Chileno).
   5. `discount`: Es un valor que indica el monto de descuento en la compra (si hubiere).
   6. `installments`: Es un objeto que contiene los valores de `quantity` (cantidad de cuotas) y `rate`(tasa de interés) para cada offer
   7. `payment_method_id`: Corresponde al método de pago que tenga el producto, en caso de estar especificado, se coloca por defecto `visa`
   8. `price`: Precio del producto.
   9. `rating`: Corresponde a la calificación del producto.
   10. `review_count`: Corresponde a la cantidad de calificaciones del producto.

# Quick Start (cómo utilizar la librería)
Si bien próximamente se desarrollará una API que cuente con un endpoint para utilizar las fucionalidades de CI-Scrapers, por el momento sólo está desarrollada la librería por lo que para poder utilizarla es necesario acceder a su código fuente y ejecutar el proceso de depuración o debugging, especificando el competidor y el o los plugins que se requieren evaluar, con los valores de entrada que requieran esos plugins seleccionados. 
Para ello, se deben seguir los siguientes pasos:
1. Prerequisitos

- Se deben contar con los permisos para poder descargar el código fuente de CI-Scrapers desde el repositorio de MELI.
- Tener instalado en el sistema, las versiones de NodeJS 14.19.2 y npm 6.4.17 o superiores
- Tener instalado un entorno de desarrollo que permita hacer depuración (Recomendado: Visual Studio Code) 

2. Instalación y Configuración
Se debe contar con la librería descargada, en su última versión. Para ello ejecutamos desde la terminal el siguiente comando Git:
```
git clone git@github.com:mercadolibre/fury_ci-scrapers.git
```
Una vez descargado el proyecto, acceder al mismo desde el entorno de desarrollo y crear la carpeta local `.vscode` dentro de la carpeta raíz del proyecto, y dentro de la misma crear el archivo `launch.json` con el siguiente código:

```json
{
    "version": "0.2.0",
    "configurations": [

        {
            "name": "ts-node",
            "type": "node",
            "request": "launch",
            "args": [
                "${relativeFile}"
            ],
            "runtimeArgs": [
                "-r",
                "ts-node/register"
            ],
            "program": "${workspaceFolder}/local/NNN.test.ts",
            "protocol": "inspector",
            "internalConsoleOptions": "openOnSessionStart"
        }
    ]
}
```
  donde `NNN`será el código del competidor a analizar (por ejemplo `FBC`para "Falabella Chile")

<img width="356" alt="vista de carpeta .vscode" src="https://user-images.githubusercontent.com/92391063/179997628-021f584c-7ab9-4619-9282-49a79a94043f.png">

A continuación se deben instalar las librerías y dependencias del proyecto
Para ello, ejecutar `npm i`

3. Configuración del competidor a analizar

Una vez configurado el ambiente de desarrollo y seleccionado el competidor, se deben configurar los parámetros que enviaremos en la consulta. Para ello, se debe acceder a la carpeta `local`,la cual posee ejemplos de consultas de todos los competidores incluídos en CI-Scrapers. Para el ejemplo anterior FBC, debemos acceder a `FBC.test.ts`. Allí se encuentran las funciones de cada uno de los plugins que maneja para el competidor. Por ejemplo para el plugin de VIP:
```ts
async function vip() {
    const vipData = await scrap({
        requestType: 'request',
        playerId: PLAYER.FBC,
        processName: PROCESS.VIP,
        itemId: '16250332'
    });
    console.log('vipData:', vipData);
}
```
En esta función se deben configurar los parámetros de entrada requeridos por la misma. De los cuatro parámetros, sólo será necesario modificar el valor de `itemId` ya que los otros siempre serán los mismos para todos los productos de este competidor. Por lo tanto debemos contar de antemano con la información del id de item para el cual queremos obtener la información detallada. 

NOTA: Para las demás funciones tampoco se deben modificar los campos `requestType`, `playerId` y `processName`
Luego de configurados los parámetros de las funciones requeridas, se deben habilitar las mismas dentro del siguiente bloque de código de este archivo:
```ts
(async () => {
    //await search();
    await vip();
    //await sellers();
    //await offers();
    //await shipping();
})();
```
Como se puede observar, sólo está habilitada la función de `vip()`, las demás se encuentran comentadas, por lo que se deben descomentar las funciones de los plugins que el usuario desee analizar. 

4. Pruebas de Scraping

Una vez realizados los pasos anteriores, podemos proceder con la prueba de scraping. La misma se hará sobre el competidor seleccionado en `launch.json`y se utilizarán los plugins habilitados en `NNN.test.ts`. Para ello ejecutamos el debug:

<img width="435" alt="start debugging in VSC" src="https://user-images.githubusercontent.com/92391063/180005034-f7c5bc10-b23b-42a4-8273-3bcd45f272cc.png">

Por consola se podrá visualizar y analizar la respuesta del competidor a la consulta. Para el ejemplo anterior, la siguiente podría ser una respuesta exitosa:

```js
vipData: {config: {…}, data: {…}, isError: false}
arg1: {config: {…}, data: {…}, isError: false}
config: {requestType: 'request', playerId: 'FBC', processName: 'vip', itemId: '16250332', version: 1}
data: {id: '16250332', own_data: {…}}
id: '16250332'
own_data: {title: 'Xiaomi Watch S1 Active Gl (Ocean Blue)', url: 'https://www.falabella.com/falabella-cl/product…iaomi-Watch-S1-Active-Gl-(Ocean-Blue)/16250332', image: 'https://falabella.scene7.com/is/image/Falabella/16250332_1', price: 149990, currency: 'CLP', …}
currency: 'CLP'
extra_data: (5) [{…}, {…}, {…}, {…}, {…}]
0: {name: 'Tipo', value: ' Smartwatch'}
1: {name: 'Género', value: ' Unisex'}
2: {name: 'Ajuste del brillo', value: ' Sí'}
3: {name: 'Alarma', value: ' Sí'}
4: {name: 'Batería', value: ' 470&nbsp;mAh'}
__proto__: Array(0)
__proto__: Object
length: 5
features: (0) []
image: 'https://falabella.scene7.com/is/image/Falabella/16250332_1'
price: 149990
title: 'Xiaomi Watch S1 Active Gl (Ocean Blue)'
url: 'https://www.falabella.com/falabella-cl/product/16250332/Xiaomi-Watch-S1-Active-Gl-(Ocean-Blue)/16250332'
__proto__: Object
__proto__: Object
isError: false
__proto__: Object

```

Como se puede observar, se obtienen diversos valores correspondientes con las características del ítem, tales como precio, título, link a la URL donde se encuentra la imagen o foto del producto, entre otros.

La salida, o lo que entrega cada plugin, corresponde a un objeto, con propiedades y subpropiedades (como `extra_data`), que poseen dentro más atributos (en este ejemplo, `extra_data` cuenta con más características del producto, que el competidor disponibilizó en su página).


## Lista de Competidores
A continuación se listan los competidores que dispone CI-Scrapers. Este proyecto está en constante actualización por lo que se siguen integrando más competidres en forma permanente.

- ALB : Aliexpréss (Brasil)
- ALK : Alkosto (Brasil)
- CEN : Centauro (Brasil)
- EXI : Éxito (Colombia)
- FBC : Falabella (Chile)
- JUM : Jumbo (Argentina)
- MDR : Madeira Madeira (Brasil)
- MUS : Musimundo (Argentina)
- SPC : Shopee (Chile)
- SPE : Shopee (Brasil)
- SPM : Shopee (México)


# Cómo Contribuir



texto 1


