Receptor : genera las dos llaves , pública y privada .
Emisor : debe conocer la llave pública .

Para no adelantar contenido generamos nosotros nuestras propias llaves.

# Generación de llaves con RSA :

### Llave privada 
<pre><code>
openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -pkeyopt rsa_keygen_pubexp:3 -out privkey-S.pem
</code></pre>

desde el directorio **claves** en la terminal 
( ID , identificador, por si es llave de github, etc )

<pre><code>
cat privkey-ID.
</code></pre>

Para mostrar más información 

<pre><code>
openssl pkey -in privkey-ID.pem -text
</code></pre>

(producto de 2 numeros primos , exponente publico y exponente privado, un primo, otro primo, ,exponentes, coeficientes , etc del algoritmo RSA)

## Generar llave pública  ( a partir de la privada )

<pre><code>
openssl pkey -in privkey-ID.pem -out pubkey-ID.pem -pubout
</code></pre>

# Para mostrar el contenido de la llave pública 

<pre><code>
openssl pkey -in pubkey-ID.pem -pubin -text
</code></pre>

Muestra el módulo y el exponente en 
(Al ser producto de 2 primos es bastante difícil factorizar )


Para el usuarion que envía ( userS(sender)) 

Los comunicantes deben contar ambos con su pareja de claves e intercambiarse las
components públicas. En la fase de generación deben ser ejecutadas las siguientes
órdenes:


<pre><code>
openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -pkeyopt rsa_keygen_pubexp:3 -out privkey-userS.pem
</code></pre>

<pre><code>
openssl pkey -in privkey-userS.pem -out pubkey-userS.pem -puboutopenssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -pkeyopt rsa_keygen_pubexp:5 -out privkey-userR.pem
</code></pre>

## Clave pública a partir de la privada 

<pre><code>
openssl pkey -in privkey-userR.pem -out pubkey-userR.pem -pubout
</code></pre>

Los usuarios intercambiamos las llaves por telegram
El receptor solo tiene la suya propia privada y la pública del emisor .


El emisor solo tiene la suya propia privada y la pública 
Si necesitara su pública la generaría a partir de la privada 

Hacemos el mensaje ( breve, que no supere la longitud e una huella hash )

#--------------------------------------------------
# Emisor

## Creamos un fichero con el mensaje ( emisor )

<pre><code>
echo Le ruego que disponga de 15540.40 dólares de
mi cuenta, a la que tiene acceso, para adquirir para mí un Bitcoin
>> message-userS.txt
</code></pre>

## Se firma ( con la privada )

<pre><code>
openssl dgst -sha256 -sign privkey-userS.pem -out message-userS.txt.sgn message-userS.txt
</code></pre>

(huella hash según el método sha256 para firmar el archivo que queremos enviar ,
message-userS.text.sgn es el archivo que me sale tras firmar  )

En el directorio userS tendríamosla pareja de claves , el mensaje y message-userS.text.sgn

## Encriptamos el mensaje (con la pública del receptor )
del usuario 

<pre><code>
openssl pkeyutl -encrypt -in message-userS.txt -pubin -inkey pubkey-userR.pem -out message-userS.txt.enc
</code></pre>

pkeutl con versiónde cifrado (-encrypt) el fichero que quiero cifrar y que cifre con la pública ( el que tenga la privada correspondiente a esa pública será quien descifrará el mensaje ) El resultado de todo esto será el mensaje cifrado message-userS.txt.enc

## Enviamos al receptor por telegram 
message-userS.text.sgn
message-userS.txt.enc

# Receptor

## Desencripta el fichero cifrado con la llave privada 

<pre><code>
openssl pkeyutl -decrypt -in message-userS.txt.enc -inkey privkey-userR.pem -out rec-message-userS.txt
</code></pre>

Hacemos cat al fichero *.txt par ver que está correctamente descifrado

## Compruebo que el emisor es quien realmente dice comprobando con la llave pública 


<pre><code>
openssl dgst -sha256 -verify pubkey-userS.pem -signature message-userS.txt.sgn rec-message-userS.txt
</code></pre>

( message-userS.txt.sgn es el que he recibido ) 
(rec-message-userS.txt es el mensaje descifrado)
( el *.txt.sgn tiene que coincidir con el que yo he descifrado rec-message-userS.txt, vemos si hay diferencias entre los menj )
( Si usamos privada para cifrar luego necesitamos la pública para encriptar ) 
( Paso intermedio : hago huella hash al descifrado my veo si coinciden )

Esperamos una verificación correcta .
y visualizamos el fichero con bash .  





