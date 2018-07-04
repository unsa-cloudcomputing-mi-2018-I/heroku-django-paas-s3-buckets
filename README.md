# Usando buckets de Amazon con Django

Los [buckets](https://docs.aws.amazon.com/AmazonS3/latest/dev/Introduction.html#BasicsBuckets) son contenedores de [objetos](https://docs.aws.amazon.com/AmazonS3/latest/dev/Introduction.html#BasicsObjects) de Amazon. Los objetos pueden ser imágenes, archivos estáticos, o  videos; tales archivos son guardados y servidos por la infraestructura de Amazon, proveyendo escalabilidad y una API simple de uso.

# Paso 1: Crear usuario en Amazon IAM

Si es que ya cuenta con un usuario, agregue las políticas de uso de s3fullacess y acceso programático como se muestra a continuación. Es importante crear un usuario que tenga los permisos de uso del servicio s3. Para lo que necesitamos crear un usuario en la consola [IAM](https://console.aws.amazon.com/iam/home?region=us-east-1) de Amazon:
Es importante brindar acceso programático para obtener las dos claves: **_access key id_** y **_secret access key_**, las cuales nos sirven para realizar operaciones de escritura y modificación programáticamente usando python.
![selection_999 048](https://user-images.githubusercontent.com/5711590/42295295-4fe7349a-7fae-11e8-96c3-83f6ee90d5f0.png)

Es importante agregar las políticas de s3 full access, o según sea conveniente:

![selection_999 049](https://user-images.githubusercontent.com/5711590/42295335-c27a2fd0-7fae-11e8-832e-d91841e60339.png)

![selection_999 050](https://user-images.githubusercontent.com/5711590/42295510-c11ca5f8-7fb0-11e8-9fd9-e3f21ad54f03.png)

Es importante guardar las dos llaves de acceso.

![selection_999 051](https://user-images.githubusercontent.com/5711590/42295573-5ca28fec-7fb1-11e8-832b-6c631582b64e.png)

# Paso 2: Crear el bucket de Amazon

Ingresar a la interfaz de [buckets](https://s3.console.aws.amazon.com/s3/home) de Amazon. 

creamos un bucket, completando los campos de nombre y la región, los demás pasos los dejamos por defecto.
![selection_999 039](https://user-images.githubusercontent.com/5711590/42295748-35cbb6b2-7fb3-11e8-82be-c0314e741cd9.png)

![selection_999 040](https://user-images.githubusercontent.com/5711590/42295757-422ee1cc-7fb3-11e8-96f0-508e8c208145.png)

Finalmente editamos la política del bucket (permisos) para que nuestro bucket sea público y pueda servir objetos en nuestras aplicaciones (modo lectura) (marcado en rojo). En el segundo _statement_ (marcado en azul) si es que queremos acceso programático coloquemos como sigue:

Debe cambiar los campos encerrados en recuadros con los suyos: Resource con su nombre de bucket, Principal - AWS (este campo se encuentra en el IAM, seleccionando el usuario creado en el paso 1).

![image](https://user-images.githubusercontent.com/5711590/42296093-13c5be06-7fb7-11e8-815a-ef337b6e729b.png)


```
{
    "Version": "2008-10-17",
    "Statement": [
        {
            "Sid": "PublicReadForGetBucketObjects",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::maestris-unsa/*"
        },
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::931228108296:user/maestria-unsa-user"
            },
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::maestris-unsa",
                "arn:aws:s3:::maestris-unsa/*"
            ]
        }
    ]
}
```
Para comprobar el correcto funcionamiento de LECTURA de sus objetos en los buckets, se debe subir un archivo a su bucket. 

![image](https://user-images.githubusercontent.com/5711590/42296193-4a136a48-7fb8-11e8-8fa6-b84ba4cce4e7.png)

Si subimos una imagen, debemos hacer un pequeño cambio en el siguiente archivo: **starterkit/common/templates/common/home.html**

```
hola mundo
<img src="https://<region>.amazonaws.com/<nombre del bucket>/<nombre del archivo>">
```
O puede hacer click en el recurso subido, y obtener la dirección url completa del recurso.

![image](https://user-images.githubusercontent.com/5711590/42296481-19cc1430-7fbc-11e8-8365-27ce2467823e.png)

podremos visualizarlo ejecutando el código del repositorio, ejecutando los siguientes comandos.

```
enrique@ex-machina:~/heroku-django-paas-starterkit$ virtualenv --python=python3.6 venv
enrique@ex-machina:~/heroku-django-paas-starterkit$ source venv/bin/activate
(venv) enrique@ex-machina:~/heroku-django-paas-starterkit$ source .env
(venv) enrique@ex-machina:~/heroku-django-paas-starterkit$ cd starterkit
(venv) enrique@ex-machina:~/heroku-django-paas-starterkit/starterkit$ python manage.py runserver
```










