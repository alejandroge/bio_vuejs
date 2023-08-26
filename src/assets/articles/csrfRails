Posted on July 3rd, 2020
En este artículo, vamos a revisar como la protección contra CSRF es implementada en Rails. Si necesitas un repaso rápido sobre que es CSRF, escribí un artículo al respecto, y lo puedes leer aquí.

En general, la gran mayoría del código responsable de este funcionamiento en Rails está en un solo archivo que puedes ver aquí en el repositorio oficial. Ahora bien, analizaremos el código responsable de esta característica en cuatro etapas, las cuales serán:

Colocar el token en la página
Incluir el token en las peticiones adecuadas
Configuración del controlador
Confirmar la validez del token
Primer etapa: Colocar el token en la página
Iniciaremos nuestro análisis con los helpers: csrf_meta_tags y token_tag. Estos dos métodos, se utilizan con el objetivo de colocar el token en la página, pero en lugares distintos.

# actionview/lib/action_view/helpers/csrf_helper.rb
def csrf_meta_tags
  if defined?(protect_against_forgery?) && protect_against_forgery?
    [
      tag("meta", name: "csrf-param", content: request_forgery_protection_token),
      tag("meta", name: "csrf-token", content: form_authenticity_token)
    ].join("\n").html_safe
  end
end
# actionview/lib/action_view/helpers/url_helper.rb
def token_tag(token = nil, form_options: {})
  if token != false && defined?(protect_against_forgery?) && protect_against_forgery?
    token ||= form_authenticity_token(form_options: form_options)
    tag(:input, type: "hidden", name: request_forgery_protection_token.to_s, value: token)
  else
    ""
  end
end
Por un lado csrf_meta_tags es llamado para incluir un meta tag en la sección <head></head> del layout principal de nuestra aplicación, mientras que token_tag se utiliza para agregar un input tag oculto en todos los formularios generados por los helpers de formulario de Rails.

Podemos observar dos aspectos comunes en ambos métodos, el primero siendo la validación de protect_against_forgery? que es la manera que ofrece Rails para activar/desactivar esta funcionalidad. Por defecto esta está activada.

El segundo aspecto común, es el uso del método form_authenticity_token, el cual vale la pena analizar como funciona.

def form_authenticity_token(form_options: {})
  masked_authenticity_token(session, form_options: form_options)
end
def masked_authenticity_token(session, form_options: {})
  action, method = form_options.values_at(:action, :method)
  raw_token = if per_form_csrf_tokens && action && method
    action_path = normalize_action_path(action)
    per_form_csrf_token(session, action_path, method)
  else
    global_csrf_token(session)
  end
  mask_token(raw_token)
end
Obteniendo el raw_token.
Existen dos alternativas para el tipo de token que recibiríamos a través de este método. La primera de ellas es la creación de un token distinto para cada formulario, utilizando el método per_form_csrf_token. Cuando se solicita un token a través del helper form_tag, obtenemos información acerca del formulario donde será utilizado. dando la oportunidad de generar un token único para determinada combinación de action y method (por ejemplo: action: 'update', method: 'post')

La otra alternativa es utilizar un token global para nuestra aplicación, el cual sería el mismo para todos los formularios y páginas de nuestra aplicación, haciendo uso del método global_csrf_token.

Para elegir que alternativa queremos utilizar, Rails proporciona la variable de configuración per_form_csrf_tokens, que puede ser modificada al nivel del controlador. Veremos más de eso en la tercer etapa.

Ahora bien, podemos ver que la implementación de ambas alternativas, no varía mucho entre ellas, ya que en realidad utilizan los mismos métodos, solamente varían en los argumentos que utilizan al invocarlos. Estos métodos son csrf_token_hmac y real_csrf_token.

def per_form_csrf_token(session, action_path, method)
  csrf_token_hmac(session, [action_path, method.downcase].join("#"))
end
GLOBAL_CSRF_TOKEN_IDENTIFIER = "!real_csrf_token"
private_constant :GLOBAL_CSRF_TOKEN_IDENTIFIER
def global_csrf_token(session) # :doc:
  csrf_token_hmac(session, GLOBAL_CSRF_TOKEN_IDENTIFIER)
end
def csrf_token_hmac(session, identifier)
  OpenSSL::HMAC.digest(
    OpenSSL::Digest::SHA256.new,
    real_csrf_token(session),
    identifier
  )
end
def real_csrf_token(session)
  session[:_csrf_token] ||= SecureRandom.base64(AUTHENTICITY_TOKEN_LENGTH)
  Base64.strict_decode64(session[:_csrf_token])
end
No es el propósito de este artículo el analizar los algoritmos criptográficos utilizados para la generación de los tokens, pero si mencionaré a un alto nivel que es lo que ocurre durante la ejecución de estos.

Podemos ver que en realidad solo existe un token único en la aplicación, independientemente de si elegimos utilizar global_token o no. La diferencia está en como se muestra el token en la página.

Al observar real_csrf_token, notamos que se genera una cadena binaria aleatoria encodeada en Base64, la cual es guardada de manera segura en la sesión. Posteriormente se decodifica dicha cadena, para tenerla en su forma binaria, debido a que dicho valor será utilizado por csrf_token_hmac, método que realiza cálculos binarios.

Para csrf_token_hmac, vemos que utilizaremos SHA256 para generar un hash que contiene nuestro token dentro. Y es aquí, donde veremos la diferencia entre global y per_form. Durante este proceso, utilizaremos, o no, la información sobre action y method a manera de salt en la generación de dicho hash.

Observamos entonces en las llamadas de per_form_token y global_csrf_token, que la diferencia entre ellas sería el identificador que es enviado como argumento. Para el primer caso, el identificador va a ser una combinación de action y method (por ejemplo para: action: 'update', method: 'post' el identificador será update#post. De esta manera se consigue que los tokens sean diferentes para cada formulario.

Para clarificar esta parte presento aquí un ejemplo de que es lo que pasa en ambos casos.

# token generado y guardado en la sesión
session[:_csrf_token] = "LwIJYZUENGVdx3FfuenxEumywQCP1KtAuzUiNkLDGh4="
# token regresado por real_csrf_token(session)
real_csrf_token = "/\x02\ta\x95\x044e]\xC7q_\xB9\xE9\xF1\x12\xE9\xB2\xC1\x00\x8F\xD4\xAB@\xBB5\"6B\xC3\x1A\x1E"
# global_csrf_token
# identifier usado '!real_csrf_token'
global_csrf_token = "/\x02\ta\x95\x044e]\xC7q_\xB9\xE9\xF1\x12\xE9\xB2\xC1\x00\x8F\xD4\xAB@\xBB5\"6B\xC3\x1A\x1E"
# per_form_token
# identifier usado 'public/update#post'
per_form_token = "\xC2\e\\\xD0\x1C\xCCW\xFD\xB0\xDF\"\x81\xA0\x95\xDE\xD0\xF6\xB3Y\x17z\xF4\xD8x\xEFR\x99_\xADx\xD1="
Enmascarando el raw_token
Ahora bien, hasta el momento tenemos un token generado en forma binaria, que no podemos colocar en la página debido a esto mismo. Debemos codificarlo primero, y para esto existe el método mask_token.

def mask_token(raw_token)
  one_time_pad = SecureRandom.random_bytes(AUTHENTICITY_TOKEN_LENGTH)
  encrypted_csrf_token = xor_byte_strings(one_time_pad, raw_token)
  masked_token = one_time_pad + encrypted_csrf_token
  Base64.strict_encode64(masked_token)
end
def xor_byte_strings(s1, s2)
  s2 = s2.dup
  size = s1.bytesize
  i = 0
  while i < size
    s2.setbyte(i, s1.getbyte(i) ^ s2.getbyte(i))
    i += 1
  end
  s2
end
Este método va a realizar dos funciones. La primera de ellas, es encriptar el token de manera que siempre sea distinto, complicando la tarea de crear un token falso.

Para esto, se utiliza un one_time_pad el cual es un código secreto que solo es utilizado una vez para encriptar un mensaje sin la intención de volver a ser usado nuevamente. Utilizando esta nueva cadena binaria, se realiza una operación XOR con el raw_token, de manera que este cambia completamente basado en este one_time_pad.

Para realizar dicha operación XOR, se escribió el método xor_byte_strings. Algo interesante a considerar aquí, es la razón por la cual se hace uso de los métodos setbyte y getbyte. Esto se debe a que Ruby maneja los objetos String bajo una codificación utf-8 por defecto. Ahora bien, en la codificación de este tipo, existen caracteres que pueden ser interpretados a partir de múltiples bytes. Debido a esto, se utilizan esos métodos para asegurar que las operaciones se realizan por cada byte, y no por cada caracter de la cadena.

Una manera rápida de visualizar esto es imprimir en la consola el raw_token generado en pasos anterior. Se observaría que parece no tener siempre la misma longitud en caracteres. La razón, la codificación utf-8 hace que algunos de los caracteres que se muestran sean interpretados a partir de más de un byte, causando esta diferencia de longitud.

Esto mismo también causa una diferencia entre los resultados de length y size. El primero nos mostraría la longitud aparente y el segundo el verdadero tamaño en bytes de la cadena.

Después de realizar la operación XOR, el one_time_pad y el token encriptado son agregados juntos para formar finalmente nuestro masked_token.

Finalmente, dicho masked_token es convertido de ser una cadena binaria a una cadena que es segura para escribir en el código HTML de nuestra página, finalizando así nuestra primer etapa del análisis.

Segunda etapa: Incluir el token en las peticiones adecuadas
Parte de esta etapa, ya se encuentra implementada por el funcionamiento por defecto de los formularios. Automáticamente cuando nosotros ejecutamos un formulario, la información reunida por este se serializa, incluyendo los campos ocultos en este. Por lo tanto, el token se incluye automáticamente. No hay otra cosa que ver aquí.

Ahora bien, el otro caso que podemos observar es el del token que se encuentra en el meta tag en la sección <head></head>. Este será utilizado básicamente, cuando se ejecute una petición asíncrona con peticiones del tipo XHR, o en el caso de los enlaces con un data-method. Estos casos, se manejan en la implementación de rails-ujs:

Enlaces con data-method
# actionview/app/assets/javascripts/rails-ujs/features/method.coffee
Rails.handleMethod = (e) ->
 ...
  href = Rails.href(link)
  csrfToken = Rails.csrfToken()
  csrfParam = Rails.csrfParam()
  form = document.createElement('form')
  formContent = "<input name='_method' value='#{method}' type='hidden' />"
  if csrfParam? and csrfToken? and not Rails.isCrossDomain(href)
    formContent += "<input name='#{csrfParam}' value='#{csrfToken}' type='hidden' />"
  ...
Este método en específico existe para manejar los enlaces generados para ejecutar acciones como delete por ejemplo. Ahora bien, si se encuentra que el CSRF token en la página, y que no se trata de una petición crossDomain (una petición fuera de nuestra aplicación), dicho token será incluido en la petición. En este caso, se genera un formulario temporal con el único objetivo de incluir en este la información necesaria.

# actionview/app/assets/javascripts/rails-ujs/utils/csrf.coffee
csrfToken = Rails.csrfToken = ->
  meta = document.querySelector('meta[name=csrf-token]')
  meta and meta.content
csrfParam = Rails.csrfParam = ->
  meta = document.querySelector('meta[name=csrf-param]')
  meta and meta.content
Aquí la definición de los métodos utilizados anteriormente, donde podemos observar que la información es tomada de los meta tags incluidos en la sección <head></head>.

Peticiones XHR
# actionview/app/assets/javascripts/rails-ujs/utils/ajax.coffee
createXHR = (options, done) ->
  ...
  unless options.crossDomain
    ...
    # Add X-CSRF-Token
    CSRFProtection(xhr)
  ...
# actionview/app/assets/javascripts/rails-ujs/utils/csrf.coffee
Rails.CSRFProtection = (xhr) ->
  token = csrfToken()
  xhr.setRequestHeader('X-CSRF-Token', token) if token?
En este caso de las peticiones XHR, se sigue una lógica bastante similar a la anterior. Cuando se trata de una petición que no es crossDomain el token será incluido, aunque esta ocasión en los encabezados (headers) de esta.

Existe además código que es mantenido con el objetivo de ser compatible con aplicaciones que hagan uso de jQuery, donde también se maneja la misma lógica de incluir los token en las peticiones que no sean crossDomain, aunque por motivos de mantener este artículo lo más corto posible no será incluido aquí.

Tercera etapa: Configuración del controlador
Finalmente, ya se generó el token y se encuentra en la página, nos hemos asegurado de incluirlo en todas las peticiones pertinentes. Es momento de configurar nuestros controladores para utilizar esta característica de Rails.

Hasta ahora, hemos mencionado un par de veces ciertas configuraciones sobre el funcionamiento de esta característica que están pensadas para hacerse directamente sobre el controlador que la utilice. Es momento de analizarlas.

Hasta el momento mencionamos dos, protect_against_forgery? y per_form_csrf_tokens, aunque en realidad existen otras más. Para este tipo de configuraciones basta con llamarlas en la definición del controlador para cambiar su valor por defecto. Un ejemplo sería como el siguiente:

class ControllerClass < ActionController
  ...
  per_form_csrf_tokens = true # por defecto es false
  # protect_against_forgery? simplemente lee este valor de configuración
  allow_forgery_protection = false # por defecto es true
  ...
end
Otro ejemplo de las configuraciones disponibles para esta característica de Rails es protect_from_forgery, la cual nos permite manejar un par de detalles directamente desde ella. Un ejemplo sería:

class ControllerClass < ActionController
  protect_from_forgery with: :exception, only: :update
  def update
    ...
  end
  ...
end
Para comprender un poco más sobre como es que esto funciona conviene revisar rápidamente la definición de dicho método.

# actionpack/lib/action_controller/metal/request_forgery_protection.rb
def protect_from_forgery(options = {})
  options = options.reverse_merge(prepend: false)
  self.forgery_protection_strategy = protection_method_class(options[:with] || :null_session)
  self.request_forgery_protection_token ||= :authenticity_token
  before_action :verify_authenticity_token, options
  append_after_action :verify_same_origin_request
end
Ahora bien, podemos observar que la opción :with es utilizada para asignar el tipo de estrategia a seguir en caso de recibir un token inválido. Por defecto Rails utiliza la estrategia de :null_session.

Posteriormente se agrega un before_action :verify_authenticity_token que utilizará las opciones pasadas como argumentos, de ahí que podamos especificar :only, para que sea solo ejecutada antes de determinadas acciones. Y finalmente, se agrega un after_action :verify_same_origin_request aunque no profundizaremos en este.

Cuarta etapa: Confirmar la validez del token
Ahora bien, si seguimos el flujo de una petición entrante al sistema, lo primero que encontraremos será la ejecución de dicho before_action, cuyo código podemos ver a continuación:

def verify_authenticity_token
  mark_for_same_origin_verification!
  if !verified_request?
    if logger && log_warning_on_csrf_failure
      if valid_request_origin?
        logger.warn "Can't verify CSRF token authenticity."
      else
        logger.warn "HTTP Origin header (#{request.origin}) didn't match request.base_url (#{request.base_url})"
      end
    end
    handle_unverified_request
  end
end
Vamos a centrarnos en los encargados de realizar la comparación del token, y dejaremos de lado la política a seguir al encontrar con un token que no es valido por el momento. Sobre dichas políticas, solo mencionaré que pueden ser configuradas en el controlador y son realizadas por el método handle_unverified_request.

Para el análisis de este artículo, nos centraremos en el método verified_request?que podemos ver a continuación:

def verified_request?
  !protect_against_forgery? || request.get? || request.head? ||
    (valid_request_origin? && any_authenticity_token_valid?)
end
Tokens que no son verificados
Si las peticiones son del tipo GET o HEAD, se considera como una petición verificada y no se continúa con la ejecución del método. En el caso de peticiones del tipo POST o DELETE, se continúa con la ejecución del método any_authenticity_token_valid? que vemos a continuación.

def any_authenticity_token_valid?
  request_authenticity_tokens.any? do |token|
    valid_authenticity_token?(session, token)
  end
end
def request_authenticity_tokens
  [form_authenticity_param, request.x_csrf_token]
end
Dentro de este método, observamos el uso de request_authenticity_tokens, los cuales son un arreglo de los posibles lugares donde pudimos haber recibido un token, a través del formulario o de un header en la petición.

Ahora bien, para cualquiera de las formas posibles de recibir el token se ejecutará el método valid_authenticity_token?que será el encargado realizar el inverso al de la primer etapa que ya analizamos. Desencriptar el token presente en la petición, y compararlo con el token real, que conservamos en la sesión.

def valid_authenticity_token?(session, encoded_masked_token)
  if encoded_masked_token.nil? || encoded_masked_token.empty? || !encoded_masked_token.is_a?(String)
    return false
  end
  begin
    masked_token = Base64.strict_decode64(encoded_masked_token)
  rescue ArgumentError # encoded_masked_token is invalid Base64
    return false
  end
  if masked_token.length == AUTHENTICITY_TOKEN_LENGTH
    ...
  elsif masked_token.length == AUTHENTICITY_TOKEN_LENGTH * 2
    csrf_token = unmask_token(masked_token)
    compare_with_global_token(csrf_token, session) ||
      compare_with_real_token(csrf_token, session) ||
      valid_per_form_csrf_token?(csrf_token, session)
  else
    false # Token is malformed.
  end
end
Este método, recibe la sesión y el token que hayamos recibido desde la petición. Si no recibimos ningún token en esta, automáticamente se considera la petición como una no verificada.

Ahora bien, si recibimos un token, debería estar codificado en Base64 como lo hicimos anteriormente para colocarlo en la página. De manera que el primer paso para verificar un token es decodificarlo a binario. Si existe un error al decodificarlo, podemos entender que dicho token no es lo que esperábamos, y se detiene el proceso de verificación ahí, considerando la petición actual como inválida.

Debido al proceso que se realizó previamente con el one_time pad, esperamos que el token que hemos recibido, tenga cierta longitud, que sería de hecho el doble de la configurada en AUTHENTICITY_TOKEN_LENGTH. Cuando el token recibido tiene una longitud distinta, confirmamos que no es un token válido.

Existe una situación, dónde el token es longitud AUTHENTICITY_TOKEN_LENGTH, el cual puede no considerarse como un token inválido. La razón, tiene que ver con la utilización o no del proceso del one_time pad. Anteriormente, esa parte del proceso, que duplica la longitud del token, no se realizaba. Al incluir esta característica, se debe dar un “tiempo de gracia” para que los tokens en la página, se actualicen a la nueva versión del doble de longitud.

Comparando el token recibido
Ahora bien, si llegamos al punto de tener un token de la longitud esperada, se continúa con el proceso de desencriptarlo, y compararlo con el token que hemos conservado en la sesión. El siguiente método que analizaremos es unmask_token.

def unmask_token(masked_token)
  one_time_pad = masked_token[0...AUTHENTICITY_TOKEN_LENGTH]
  encrypted_csrf_token = masked_token[AUTHENTICITY_TOKEN_LENGTH..-1]
  xor_byte_strings(one_time_pad, encrypted_csrf_token)
end
Podemos ver que en este método, se realiza el proceso inverso al método masked_token. Usando la segunda parte del token que recibimos, obtenemos la versión sin la máscara, realizando una operación XOR nuevamente. Esto debido a que debemos recordar, que si se realiza la operación XOR dos veces seguidas, se obtiene el mismo resultado.

Por ejemplo:

1011 XOR 1111 = 0100
0100 XOR 1111 = 1011
Una ves que se obtiene el token, sin el one_time_pad, es momento de comparar el valor que se tiene actualmente, con el valor que se guardó inicialmente en la sesión del usuario, y para eso tenemos tres diferentes posibilidades para autorizar un token

compare_with_global_token(csrf_token, session) ||
  compare_with_real_token(csrf_token, session) ||
  valid_per_form_csrf_token?(csrf_token, session)
La diferencia entre los métodos anteriores, solamente es el formato del token con el cual se compara. Puede ser con el token global, el cual ha sido combinado con el salt !real_csrf_token, o con el token almacenado como tal en la session, o con el per_form_token.

def compare_with_real_token(token, session)
  ActiveSupport::SecurityUtils.fixed_length_secure_compare(token, real_csrf_token(session))
end
def compare_with_global_token(token, session)
  ActiveSupport::SecurityUtils.fixed_length_secure_compare(token, global_csrf_token(session))
end
def valid_per_form_csrf_token?(token, session)
  if per_form_csrf_tokens
    correct_token = per_form_csrf_token(
      session,
      normalize_action_path(request.fullpath),
      request.request_method
    )
    ActiveSupport::SecurityUtils.fixed_length_secure_compare(token, correct_token)
  else
    false
  end
end
Un aspecto interesante a comentar sobre estos métodos, es la utilización del método ActiveSupport::SecurityUtils.fixed_length_secure_compare el cual hace una comparación entre dos Strings. La única diferencia con una comparación normal es que este método recorre la longitud completa de estas siempre, aún cuando ambas sean completamente diferentes. Esto asegura que no pueda utilizarse el tiempo en que se tarda en responder la función con fines de encontrar el token con un algoritmo que tome este en cuenta.

Y así llegamos al final de este análisis, y también al final del análisis de qué es Cross Site Request Forgery. En el siguiente artículo, tomaremos una ruta un poco distinta y analizaremos los tipos de datos usados en PostgreSQL, y ejemplos de su utilización.
