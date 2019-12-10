
## IBM Developer Advocates Team
[![IBM Cloud powered][img-ibmcloud-powered]][url-ibmcloud]

# Functions de IBM Cloud 
[Functions](#Functions)
Plataforma de IBM cloud de programacion poliglota FaaS (Functions-as-a-Service) para desarrollo de codigo ligero que escala dependiendo de la demanda

GitHub de Serverless para desplegar con githubpages: [![Serverless][img-cloud-serverless]][url-githubserverless]

Para mayor informacion: [![Functions][img-cloud-functions]][url-ibmcloud-Functions]

# IBM Cloud App ID 
[AppId](#Appid)
Servicio de IBM cloud que de manera sencilla agrega autentificacion, asegura backends y APIs, y gestiona datos de usuarios especificos en una aplicacion web y movil

Para mayor informacion: [![App-id][img-cloud-appid]][url-ibmcloud-appid]

# IBM Cloudant
Es una base de datos completamente administrable para aplicaciones multinube hibrida con una completa compatibilidad de API.

Para mayor informacion: [![Cloudant][img-cloud-cloudant]][url-ibmcloud-cloudant]

# IBM Cloud Object Storage

Para mayor informacion: [![Object][img-cloud-object]][url-ibmcloud-object]

## Prework:
* Cuenta de [IBM Cloud][url-IBMCLOUD]
* Instalar [CLI de IBM Cloud][url-CLI-IBMCLOUD] 
* Cuenta en [GitHub][url-github-join]
* Instalar [CLI de GitHub][url-github-cli] o instalar [GitHub Desktop][url-githubdesktop]
* [NodeJS][url-node]
* Utilizar safari, chrome, firefox, edge

### Cupones para Estudiantes y profesores
 

	1.	Acceder al HUB para Software para uso académico. Y navegar hasta la parte de abajo de la pagina https://onthehub.com/ibm/?utm_sourc=ibm-ai-productpage&utm_medium=onthehubproductpage&utm_campaign=IBM 
	2.	Buscar el WebStore del instituto/escuela al que perteneces. 
	3.	En caso de no contar con WebStore, acceder al portal de IBM Academic Initiative y seleccionar la opción de <Students> 
	4.	Seleccionar <Add to Cart> para IBM Bluemix – 6 Month Trial. 
	5.	Realizar el registro correspondiente utilizando la cuenta de correo académica 
 
### Cargar créditos en IBM Cloud
	1.	Ingresamos a nuestro panel de control de IBM Cloud (console.bluemix.net)
	2.	Una vez que tengamos el código nos vamos a Gestionar>Facturación y Uso>Facturación
	3.	Buscamos “Códigos de características (Promocionales)”


## Contenido

`app.js`  Usa Express para establecer rutas.

`public/index.html`  La pagina de destino de la aplicacion. Hacer click en **Iniciar sesion** para iniciar.

`protected/protected.html`  La pagina protegida de la aplicacion. Despues de hacer click en el boton de **Iniciar sesion**, el usuario es redrigido aqui. Aqui es donde se revisa si el usuario esta autorizado o no. En el caso en el que el usuario no este autorizado, se envia una solicitud al servidor de autenticacion para iniciar el flow de el OAuth. Si el usuario esta autorizado, se muestra la pagina y la informacion protegida.


## Cloudant
En esta seccion iniciaremos un servicio de Base de Datos no relacional.
1. Buscaremos en nuestro catalogo **Cloudant**.
2. Seleccionaremos el Plan **Lite**, le asignamos un nombre y en los metodos de autentificacion seleccionamos **Use both legacy credentials and IAM** y creamos el servicio.
3. Ya que el servicio este suministrado, ingresamos al servicio y damos click en **Launch Cloudant Dashboard** para que nos abre una pestaña nueva donde podremos ver nuestra base de datos, podemos tener mas de una base de datos con difernete nombre dentro del servicio.
4. Crearemos una base de datos nueva en el apartado superior derecho **Create Database**, crearemos 4 que son:
    1. guestbook. Donde guardaremos los comentarios en vivo
    2. institucion. Donde cargaremos una lista que es parte de el Registro de Actividades
    3. teach_adv. Donde se guardaran las actividades
    4. pictures_teach_adv. Donde se guardaran imagenes como reporte de actividades

# Functions

## Configuración de Functions
Comentarios en Vivo
![LIVEFEED](/docs/LIVEFEED.png)
En esta sección configuraremos nuestro servicio de Functions.
1. Secuencia de acciones para escribir a la base de datos
	1. Vamos al catálogo y buscamos Cloud Functions
 	2. Una vez dentro seleccionamos Actions
	3. Damos click en Create
	5. Damos click en Create action
	6. Ponemos el nombre prepare-entry-for-save y seleccionamos Node.js 6 como el Runtime, damos click en Create
	7. Cambiamos el código por el siguiente:
		``` js
		function main(params) {
		 if (!params.nombre || !params.comentario) {
		  return Promise.reject({ error: 'no nombre or comentario'});
		  }
		 return {
		  doc: {
		   createdAt: new Date(),
		   nombre: params.nombre,
		   correo: params.correo,
		   comentario: params.comentario
		  }
		 };
	 	}
		```
	8. Lo salvamos
	9. Para añadir nuestra acción a una secuencia primero nos vamos al tab “Enclosing Secuences” y damos click en “Add to Sequence”
 	10.	Para el nombre de la secuencia ponemos save-guestbook-entry-sequence y posteriormente damos click en Create and Add
	11.	Una vez que esta creada nuestra secuencia le damos click y damos click en Add posteriormente
 	12.	Damos click en Use Public y seleccionamos Cloudant
 	13.	Seleccionamos la acción create-document, damos click en New Binding, ponemos de nombre de nuestro paquete binding-for-guestbook y en Cloudant Instance seleccionamos Input Your Own Credentials
 	14.	 Para llenar todos los datos posteriores copiamos lo que teníamos en el servicio de Cloudant como credenciales y damos click en Add:
 	15.	Para probar que esté funcionando, damos click en change input e ingresamos nuestro siguiente JSON y damos click en Apply y luego en Invoke
	 ```json
		{
		"nombre": "John Smith",
		"correo": "john@smith.com",
		"comentario": "this is my comment"
		}
	```
	Una vez hecho esto podremos verlo escrito en nuestra base de datos de Cloudant en la sección Documents
 
2. Secuencia de acciones para obtener las entradas de la base de datos
Esta secuencia la usaremos para tomar las entradas de cada usuario y sus respectivos comentarios
	1.	En nuestra tab de functions creamos una acción Node.js y le ponemos el nombre set-read-input, siguiendo el mismo proceso que en la acción anterior
	2.	Reemplazamos el código que viene, esta acción pasa los parámetros apropiados a nuestra siguiente acción
		```js
		function main(params) {
		 return {
		  params: {
		   include_docs: true
		   }
		 };
		}
		```
	3. Damos click en Save 
	4. Damos click en Enclosing Sequences, Add to Sequence y Create New con el nombre read-guestbook-entries-sequence damos click en Create and Add
	5. Damos click en Actions y  damos click en read-guestbook-entries-sequence
 	6. Damos click en Add para crear una segunda acción en la secuencia
	7. Seleccionamos Public y Cloudant
 	8.	Seleccionamos list-documents en actions y seleccionamos el binding binding-for-guestbook y posteriormente damos click en Add
 	9.	Damos click en Add para añadir una acción más a la secuencia, esta es la que va a dar el formato de los documentos cuando regresen de Cloudant
	10.	La nombraremos format-entries y posteriormente damos click en Create and add 
	11.	Damos click en format-entries y reemplazamos el código con:
		```JS
		const md5 = require('spark-md5');
			
		function main(params) {
		 return {
		  entries: params.rows.map((row) => { return {
		   nombre: row.doc.nombre,
		   correo: row.doc.correo,
		   comentario: row.doc.comentario,
		   createdAt: row.doc.createdAt,
		   icon: (row.doc.correo ? `https://secure.gravatar.com/avatar/${md5.hash(row.doc.correo.trim().toLowerCase())}?s=64` : null)
		  }})
		 };
		}
		```
	12.	Salvamos y damos click en invoke
 
## Configurar el API
1.	Dentro de nuestras acciones seleccionamos nuestras secuencias y en la tab de Endpoints damos click en Enable Web Action y damos click en Save
 
2.	Nos vamos a Functions y damos click en APIs
 
3.	Damos click en Create Managed API
4.	En el API name ponemos guestbook y en el path ponemos /guestbook y damos click en create operation
 
5.	Creamos un path que sea /entries ponemos el verbo a GET y seleccionamos la secuencia read-guestbook-entries-sequence y damos click en Create
 
6.	Realizamos la misma acción pero ahora con un POST y la secuencia save-guestbook-entries-sequence y damos click en Create
7.	Salvamos y exponemos la API
8. Copiamos el link expuesto y lo cambiamo en ./protected/javascript/guestbook.js
    ``` js
    const apiUrl = '<url-api-functions>';
    ```
    en la linea 4
    ![link](/docs/ApiGuestbook.png)

Para el registro de Actividades
![LIVEFEED](/docs/Actividades.png)


    

# AppId

## Tabla de Contenidos
* [Contenido](#Contenido)
* [Requerimientos](#Requerimientos)
* [Ejecucion local](#Ejecucion-local)
* [Ejecucion en IBM Cloud](#Ejecucion-en-IBM-cloud)
* [Licencia](#Licencia)

(falta agregar configuraciones de appid en esta parte)
## Ejecucion local

Ejecuta los siguientes comandos:
```bash
npm install
npm start
```
Usa el link http://localhost:3000 para cargar la aplicacion web en el navegador.

## Ejecucion en IBM Cloud

### Deployment

**Important:** Before going live, remove http://localhost:3000/* from the list of web redirect URLs located in "Manage Authentication" -> "Authentication Settings" page in the AppID dashboard.

1. Login to IBM Cloud.

  `ibmcloud login -a https://api.{{domain}}`

2. Target a Cloud Foundry organization and space in which you have at least Developer role access:

  Use `ibmcloud target --cf` to target Cloud Foundry org/space interactively.

3. Bind the sample app to the instance of App ID:

  `ibmcloud resource service-alias-create "appIDInstanceName-alias" --instance-name "appIDInstanceName" -s {{space}}`
  
4. Add the alias to the manifest.yml file in the sample app.

   ```
   applications:
        - name: [app-instance-name]
        memory: 256M
        services:
        - appIDInstanceName-alias
   ```

5. Deploy the sample application to IBM Cloud. From the app's folder do:

  `ibmcloud app push`
  
6. Now configure the OAuth redirect URL at the App ID dashboard so it will approve redirecting to your cluster. Go to your App ID instance at [IBM Cloud console](https://cloud.ibm.com/resources) and under Manage Authentication->Authentication Settings->Add web redirect URLs add the following URL:

   `https://{App Domain}/ibm/cloud/appid/callback`
   
   You find your app's domain by visiting Cloud Foundry Apps at the IBM Cloud dashboard: https://cloud.ibm.com/resources.

7. Open your IBM Cloud app route in the browser.

## Running in Kubernetes

### Prerequisites
Before you begin make sure that IBM Cloud CLI, docker and kubectl installed and that you have a running kubernetes cluster.
You also need an IBM Cloud container registry namespace (see https://cloud.ibm.com/kubernetes/registry/main/start). You can find your registry domain and repository namespace using `ibmcloud cr namespaces`.

### Deployment

**Important:** Before going live, remove http://localhost:3000/* from the list of web redirect URLs located in "Manage Authentication" -> "Authentication Settings" page in the AppID dashboard.

**Note:** Your App ID instance name must consist of lower case alphanumeric characters, '-' or '.', and must start and end with an alphanumeric character. You can visit the App ID dashboard to change your instance name. 

1. Login to IBM Cloud.

    `ibmcloud login -a https://api.{{domain}}`
  
2. Run the following command, it will output an export command.

    `ibmcloud cs cluster-config {CLUSTER_NAME}`
    
3. Set the KUBECONFIG environment variable. Copy the output from the previous command and paste it in your terminal. The command output looks similar to the following example:
   
    `export KUBECONFIG=/Users/$USER/.bluemix/plugins/container-service/clusters/mycluster/kube-config-hou02-mycluster.yml`

4. Bind the instance of App ID to your cluster.

    `ibmcloud cs cluster-service-bind {CLUSTER_NAME} default {APP_ID_INSTANCE_NAME}`
    
5. Find your cluster's public endpoint {CLUSTER_ENDPOINT}.
   
   Note: If you are using the free version of kubernetes (with only 1 worker node) you can use your node's public IP instead, which you can find using:

    `ibmcloud cs workers {CLUSTER_NAME}`

6. Edit the kube_deployment.yml file. 
    1. Edit the image field of the deployment section to match your image name. The name of your image should be `{REGISTRY_DOMAIN}/{REPOSITORY_NAMESPACE}/appid-node-sample:{APP_VERSION}`). 
    2. Edit the Binding name field to match yours. It should be `binding-{APP_ID_INSTANCE_NAME}`.
    3. Edit redirectUri's value to include your cluster's IP. The value should be `http://{CLUSTER_ENDPOINT}/ibm/cloud/appid/callback`
    4. Optional: Change the value of metadata.namespace from default to your cluster namespace if you’re using a different namespace.

7. Build your Docker image.
   
    `docker build -t {REGISTRY_DOMAIN}/{REPOSITORY_NAMESPACE}/appid-node-sample:{APP_VERSION} .`
    
8. Push the image.
   
    `docker push {REGISTRY_DOMAIN}/{REPOSITORY_NAMESPACE}/appid-node-sample:{APP_VERSION}`
   
    `kubectl apply -f kube_deployment.yml`

9. Now configure the OAuth redirect URL at the App ID dashboard so it will approve redirecting to your cluster. Go to your App ID instance at [IBM Cloud console](https://cloud.ibm.com/resources) and under Manage Authentication->Authentication Settings->Add web redirect URLs add the following URL:

   `https://{CLUSTER_ENDPOINT}:30000/ibm/cloud/appid/callback`

10. You can see your sample running on Kubernetes in IBM Cloud.
   
    `open http://{CLUSTER_ENDPOINT}:30000`

## See More
#### Protecting Node.js Web Applications with IBM Cloud App ID
https://www.youtube.com/watch?v=6roa1ZOvwtw

## Licencia

Copyright (c) 2019 IBM Corporation

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

[img-ibmcloud-powered]: https://img.shields.io/badge/IBM%20cloud-powered-blue.svg
[url-ibmcloud-Functions]: https://www.ibm.com/cloud/functions
[url-ibmcloud-object]: https://www.ibm.com/cloud/object-storage
[img-cloud-functions]: https://img.shields.io/badge/IBM%20cloud-Functions-red.svg
[img-cloud-object]: https://img.shields.io/badge/IBM%20cloud-Object-red.svg
[img-cloud-serverless]: https://img.shields.io/badge/IBM%20cloud-Serverless-green.svg
[img-cloud-appid]: https://img.shields.io/badge/IBM%20cloud-AppID-red.svg
[img-cloud-cloudant]: https://img.shields.io/badge/IBM%20cloud-Cloudant-red.svg
[url-ibmcloud-appid]: https://www.ibm.com/cloud/app-id
[url-ibmcloud-cloudant]: https://www.ibm.com/cloud/cloudant
[url-ibmcloud]: https://www.ibm.com/cloud/
[url-githubserverless]: https://github.com/ibmdevelopermx/Serverless_Cloudant
[url-IBMCLOUD]: https://cloud.ibm.com/registration
[url-CLI-IBMCLOUD]: https://cloud.ibm.com/docs/cli/reference/ibmcloud?topic=cloud-cli-install-ibmcloud-cli
[url-github-join]: https://github.com/join
[url-github-cli]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
[url-githubdesktop]: https://desktop.github.com/
[url-node]: https://nodejs.org/es/download/