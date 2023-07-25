---
nav_title: Límites de tarifa
article_title: Límites de la tasa API
orden_página: 4.5
descripción: "Este artículo de referencia cubre los límites de tasa API para la infraestructura API Braze".
tipo_página: referencia

---

# Límites de tarifa

> La infraestructura de la API Braze está diseñada para gestionar grandes volúmenes de datos en toda nuestra base de clientes. Para ello, aplicamos límites de velocidad de API por espacio de trabajo. 

Un límite de velocidad es el número de peticiones que la API puede recibir en un periodo de tiempo determinado. Muchos incidentes de denegación de servicio basados en la carga en grandes sistemas son involuntarios, causados por errores en el software o las configuraciones, no por ataques maliciosos. Los límites de velocidad garantizan que dichos errores no priven a nuestros clientes de los recursos de la API Braze. Si se envían demasiadas solicitudes en un periodo de tiempo determinado, es posible que vea respuestas de error con un código de estado `429`, que indica que se ha alcanzado el límite de velocidad.

{% alert warning %}
Los límites de velocidad de la API están sujetos a cambios en función del uso adecuado de nuestro sistema. Recomendamos límites razonables al realizar una llamada a la API para evitar daños o usos indebidos.
{% endalert %}

## Límites de tarifas por tipo de solicitud

La siguiente tabla enumera los límites de velocidad por defecto de la API para diferentes tipos de solicitudes. Todas las demás solicitudes que no figuran en esta tabla tienen un límite de velocidad predeterminado de 250.000 solicitudes por hora. 

Estos límites predeterminados se pueden aumentar a pedido. Comuníquese con su gerente de éxito del cliente para obtener más información.

| Tipo de solicitud | Límite de tasa de API predeterminado |
| --- | --- |
| [`/users/track`][10] | **Solicitudes:** 50.000 solicitudes por minuto.<br><br>**Por lotes:** 75 eventos, 75 compras y 75 atributos por solicitud de API. Ver [Batching User Track requests](#batch-user-track) para más información. |
| [`/users/export/ids`][11] | 2.500 peticiones por minuto. |
| [`/users/delete`][12] <br> [`/users/alias/new`][13] <br> [`/users/alias/update`][45] <br> [`/users/identify`][14] <br> [`/users/merge`][44] | 20.000 solicitudes por minuto, compartidas entre los puntos finales. |
| [`/users/external_id/rename`][20] | 1.000 peticiones por minuto. |
| [`/users/external_id/remove`][21] | 1.000 peticiones por minuto. |
| [`/events/list`][15] | 1.000 peticiones por hora, compartidas con el endpoint `/purchases/product_list`. |
| [`/purchases/product_list`][16] | 1.000 peticiones por hora, compartidas con el endpoint `/events/list`. |
| [`/messages/send`][17] | 250 peticiones por minuto para llamadas de difusión (cuando sólo se especifica un segmento o Audiencia Conectada). En caso contrario, 250.000 solicitudes por hora. |
| [`/campaigns/trigger/send`][17.1] | 250 solicitudes por minuto para llamadas de difusión (cuando sólo se especifica un segmento o Audiencia Conectada). En caso contrario, 250.000 solicitudes por hora. |
| [`/canvas/trigger/send`][17.2] | 250 peticiones por minuto para llamadas de difusión (cuando sólo se especifica un segmento o Audiencia Conectada). En caso contrario, 250.000 solicitudes por hora. |
| [`/sends/id/create`][18] | 100 peticiones al día. |
| [`/subscription/status/set`][19] | 5.000 peticiones por minuto. |
| [`/preference_center/v1/{preferenceCenterExternalId}/url/{userId}`][26]<br>[`/preference_center/v1/list`][27]<br>[`/preference_center/v1/{preferenceCenterExternalId}`][28] | 1.000 solicitudes por minuto, por espacio de trabajo. |
| [`/preference_center/v1`][29]<br>[`/preference_center/v1/{preferenceCenterExternalId}`][30] | 10 solicitudes por minuto, por espacio de trabajo. |
| [`/catalogs/{catalog_name}`][31]<br>[`/catalogs`][32]<br>[`/catalogs`][33] | 5 peticiones por minuto compartidas entre los endpoints. |
| [`/catalogs/{catalog_name}/items`][34]<br>[`/catalogs/{catalog_name}/items`][35]<br>[`/catalogs/{catalog_name}/items`][36] | 100 peticiones por minuto compartidas entre los endpoints. |
| [`/catalogs/{catalog_name}/items/{item_id}`][37]<br>[`/catalogs/{catalog_name}/items/{item_id}`][38]<br>[`/catalogs/{catalog_name}/items`][39]<br>[`/catalogs/{catalog_name}/items/{item_id}`][40]<br>[`/catalogs/{catalog_name}/items/{item_id}`][41] | 50 peticiones por minuto compartidas entre los puntos finales. |
| [`/scim/v2/Users/{id}`][22]<br>[`/scim/v2/Users?filter={userName@example.com}`][43]<br>[`/scim/v2/Users/{id}`][25]<br>[`/scim/v2/Users/{id}}`][24]<br>[`/scim/v2/Users/`][23] | 5.000 solicitudes al día, por empresa, compartidas entre los endpoints. |
{: .reset-td-br-1 .reset-td-br-2}

## Solicitudes API por lotes

Las API de Braze están diseñadas para soportar el procesamiento por lotes. Con el procesamiento por lotes, Braze puede recibir tantos datos como sea posible en una sola llamada a la API para que no tenga que hacer muchas llamadas a la API. Es más eficaz para Braze procesar datos por lotes que procesar datos de una llamada cada vez. Por ejemplo, gestionar 1.000 llamadas API por lotes requiere menos recursos que gestionar 75.000 llamadas individuales. El procesamiento por lotes es extremadamente importante para cualquier aplicación que pueda requerir más de 75.000 llamadas por hora.

{% alert note %}
Los aumentos del límite de tarifa de la API REST se consideran en función de la necesidad de los clientes que hacen uso de las capacidades de lote de la API.
{% endalert %}

### Batching User Track requests {#batch-user-track}

Cada solicitud `/users/track` puede contener hasta 75 objetos de evento, 75 objetos de atributo y 75 objetos de compra. Cada objeto (matrices de eventos, atributos y compras) puede actualizar un usuario cada uno. En total, esto significa que se puede actualizar un máximo de 225 usuarios en una sola llamada. Además, un mismo perfil de usuario puede ser actualizado por varios objetos.

Las solicitudes realizadas a este punto final generalmente comenzarán a procesarse en este orden: 

1. Atributos
2. Eventos
3. Compras

### Batching Messaging endpoint requests

Una única solicitud a los [puntos finales de mensajería][1] puede llegar a cualquiera de los siguientes:

- Hasta 50 `external_ids` específicos, cada uno con parámetros de mensaje individuales
- Un segmento de cualquier tamaño creado en el cuadro de mandos de Braze, especificado por su `segment_id`.
- Un segmento de audiencia ad hoc de cualquier tamaño, definido en la solicitud como un objeto [Audiencia conectada][2]

## Control de los límites de tarifa

Cada solicitud de API enviada a Braze devuelve la siguiente información en las cabeceras de respuesta:

Cabecera Nombre | Descripción
----------------------- | -----------------------
`X-RateLimit-Limit` | El número máximo de solicitudes que puede realizar en un intervalo especificado (su límite de velocidad).
`X-RateLimit-Remaining` | El número de solicitudes restantes en la ventana de límite de velocidad actual.
`X-RateLimit-Reset`     | El momento en el que se restablece el límite de tasa actual en segundos de época UTC.
{: .reset-td-br-1 .reset-td-br-2}

Esta información se incluye intencionadamente en el encabezado de la respuesta a la solicitud de API en lugar de en el panel de control de Braze. Esto permite que su sistema reaccione mejor en tiempo real a medida que interactúa con nuestra API. Por ejemplo, si el valor `X-RateLimit-Remaining` desciende por debajo de un determinado umbral, es posible que desee ralentizar el envío para asegurarse de que se envían todos los correos electrónicos transaccionales. O, si llega a cero, es posible que desee detener todos los envíos hasta que transcurra el tiempo especificado en "X-RateLimit-Reset".

Si tiene alguna pregunta sobre los límites de la API, póngase en contacto con su gestor de éxito del cliente o abra un [ticket de soporte][support].

### Retardo óptimo entre puntos finales

{% alert note %}
Le recomendamos que deje transcurrir 5 minutos entre llamadas consecutivas a los terminales para minimizar los errores.
{% endalert %}

Comprender el retardo óptimo entre puntos finales es crucial cuando se realizan llamadas consecutivas a la API Braze. Los problemas surgen cuando los puntos finales dependen del procesamiento correcto de otros puntos finales y, si se llaman demasiado pronto, podrían producirse errores. Por ejemplo, si está asignando a los usuarios un alias a través de nuestro punto final `/user/alias/new` y, a continuación, utiliza ese alias para enviar un evento personalizado a través de nuestro punto final `/users/track`, ¿cuánto tiempo debe esperar?

En condiciones normales, el tiempo para que se produzca la consistencia eventual de nuestros datos es de 10-100ms (1/10 de segundo). Sin embargo, puede haber algunos casos en los que esa consistencia tarde más tiempo en producirse, por lo que te recomendamos que preveas un retraso de 5 minutos entre la realización de llamadas posteriores para minimizar la probabilidad de error.

[1]: {{site.baseurl}} /api/puntos finales/mensajería/
[2]: {{site.baseurl}} /api/objects_filters/connected_audience/
[soporte]: {{site.baseurl}}/braze_support/
[10]: {{site.baseurl}}/api/endpoints/user_data/post_user_track/
[11]: {{site.baseurl}}/api/endpoints/export/user_data/post_users_identifier/
[12]: {{site.baseurl}}/api/endpoints/user_data/post_user_delete/
[13]: {{site.baseurl}}/api/endpoints/user_data/post_user_alias/
[14]: {{site.baseurl}}/api/endpoints/user_data/post_user_identify/
[15]: {{site.baseurl}}/api/endpoints/export/custom_events/get_custom_events/
[16]: {{site.baseurl}}/api/endpoints/export/purchases/get_list_product_id/
[17]: {{site.baseurl}}/api/endpoints/messaging/send_messages/post_send_messages/
[17.1]: {{site.baseurl}}/api/endpoints/messaging/send_messages/post_send_triggered_campaigns/
[17.2]: {{site.baseurl}}/api/endpoints/messaging/send_messages/post_send_triggered_canvases/
[18]: {{site.baseurl}}/api/endpoints/messaging/send_messages/post_create_send_ids/
[19]: {{site.baseurl}}/api/endpoints/subscription_groups/post_update_user_subscription_group_status/
[20]: {{site.baseurl}}/api/endpoints/user_data/external_id_migration/post_external_ids_rename/
[21]: {{site.baseurl}}/api/endpoints/user_data/external_id_migration/post_external_ids_remove/
[22]: {{site.baseurl}}/get_see_user_account_information/
[23]: {{site.baseurl}}/post_create_user_account/
[24]: {{site.baseurl}}/delete_existing_dashboard_user/
[25]: {{site.baseurl}}/post_update_existing_user_account/
[26]: {{site.baseurl}}/api/endpoints/preference_center/get_create_url_preference_center/
[27]: {{site.baseurl}}/api/endpoints/preference_center/get_list_preference_center/
[28]: {{site.baseurl}}/api/endpoints/preference_center/get_view_details_preference_center/
[29]: {{site.baseurl}}/api/endpoints/preference_center/post_create_preference_center/
[30]: {{site.baseurl}}/api/endpoints/preference_center/put_update_preference_center/
[31]: {{site.baseurl}}/api/endpoints/catalogs/catalog_management/synchronous/delete_catalog/
[32]: {{site.baseurl}}/api/endpoints/catalogs/catalog_management/synchronous/get_list_catalogs/
[33]: {{site.baseurl}}/api/endpoints/catalogs/catalog_management/synchronous/post_create_catalog/
[34]: {{site.baseurl}}/api/endpoints/catalogs/catalog_items/asynchronous/delete_catalog_items_bulk/
[35]: {{site.baseurl}}/api/endpoints/catalogs/catalog_items/asynchronous/patch_catalog_items_bulk/
[36]: {{site.baseurl}}/api/endpoints/catalogs/catalog_items/asynchronous/post_create_catalog_items_bulk/
[37]: {{site.baseurl}}/api/endpoints/catalogs/catalog_items/synchronous/delete_catalog_item/
[38]: {{site.baseurl}}/api/endpoints/catalogs/catalog_items/synchronous/get_catalog_item_details/
[39]: {{site.baseurl}}/api/endpoints/catalogs/catalog_items/synchronous/get_catalog_items_details_bulk/
[40]: {{site.baseurl}}/api/endpoints/catalogs/catalog_items/synchronous/patch_catalog_item/
[41]: {{site.baseurl}}/api/endpoints/catalogs/catalog_items/synchronous/post_create_catalog_item/
[43]: {{site.baseurl}}/get_search_existing_dashboard_user_email/
[44]: {{site.baseurl}}/api/endpoints/user_data/post_users_merge/
[45]: {{site.baseurl}}/api/endpoints/user_data/post_users_alias_update/
