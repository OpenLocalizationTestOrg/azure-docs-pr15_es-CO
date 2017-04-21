<properties
    pageTitle="Introducción al almacenamiento | Microsoft Azure"
    description="Visión general del almacenamiento de Azure, almacenamiento de datos en línea de Microsoft en la nube. Aprenda a utilizar la mejor solución de almacenamiento de nube disponibles en las aplicaciones."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="introduction-to-microsoft-azure-storage"></a>Introducción al almacenamiento de Azure de Microsoft

## <a name="overview"></a>Información general

Almacenamiento de Azure es la solución de almacenamiento de nube para aplicaciones modernas que dependen de durabilidad, disponibilidad y escalabilidad para satisfacer las necesidades de sus clientes. Leyendo este artículo, los desarrolladores, profesionales de TI y Decisión empresarial pueden aprender los fabricantes:

- ¿Qué es el almacenamiento de Azure y cómo puede aprovechar las ventajas en la nube, móvil, servidor y aplicaciones de escritorio
- ¿Qué tipos de datos puede almacenar con los servicios de almacenamiento de Azure: blob de datos (objeto), datos de la tabla de NoSQL, cola de mensajes y recursos compartidos de archivos.
- ¿Cómo se administra el acceso a los datos en el almacenamiento de Azure
- Como los datos de almacenamiento de Azure se hace duraderos a través de redundancia y replicación
- Dónde ir a continuación para crear su primera aplicación de almacenamiento de Azure

Para poner en funcionamiento con el almacenamiento de Azure rápidamente, consulte [Introducción a almacenamiento de Azure en cinco minutos](storage-getting-started-guide.md).

Para obtener más información sobre herramientas, bibliotecas y otros recursos para trabajar con el almacenamiento de Azure, consulte los [Pasos siguientes](#next-steps) .

## <a name="what-is-azure-storage"></a>¿Qué es el almacenamiento de Azure?

Cloud computing permite nuevos escenarios para aplicaciones que requieren almacenamiento de información escalable, duradero y altamente disponible para sus datos, que es exactamente por qué Microsoft desarrolló el almacenamiento de Azure. Además de lo que permite a los programadores generar aplicaciones a gran escala para soportar nuevos escenarios, almacenamiento Azure proporciona la base del almacenamiento de Azure máquinas virtuales, un testimonio más de su solidez.

Almacenamiento de Azure es escalable, por lo que puede almacenar y proceso de cientos de terabytes de datos para admitir escenarios de datos grande requerido por análisis científicos, financieros y aplicaciones multimedia. O bien, puede almacenar las pequeñas cantidades de datos necesarios para un sitio Web de la pequeña empresa. Donde se encuentran sus necesidades, puede pagar sólo los datos que se almacena. Almacenamiento de Azure actualmente almacena decenas de billones de objetos de cliente único y controla millones de solicitudes por segundo en promedio.

Almacenamiento de Azure es elástico, por lo que puede diseñar aplicaciones para una amplia audiencia global y escalar las aplicaciones según sea necesario - tanto en términos de la cantidad de datos almacenados y el número de solicitudes realizadas contra él. Paga sólo por lo que utiliza, y sólo cuando se utiliza.

Almacenamiento de Azure usa un sistema automático de la partición que automáticamente equilibra la carga de los datos basados en el tráfico. Esto significa que como las demandas de crecimiento de su aplicación, el almacenamiento de Azure asigna automáticamente los recursos adecuados para satisfacerlos.

Almacenamiento de Azure es accesible desde cualquier lugar del mundo, desde cualquier tipo de aplicación, si se está ejecutando en la nube, en el escritorio, en un servidor local o en un móvil o dispositivo de tableta gráfica. Puede utilizar almacenamiento de Azure en escenarios móviles, donde la aplicación almacena un subconjunto de datos en el dispositivo y sincroniza con un conjunto completo de datos almacenados en la nube.

Almacenamiento de Azure es compatible con clientes que utilizan un diverso conjunto de sistemas operativos (incluido Windows y Linux) y una variedad de lenguajes de programación (incluyendo. NET, Java, Node.js, Python, Ruby, PHP y C++ y los lenguajes de programación móviles) para el desarrollo de la práctica. Almacenamiento de Azure también expone recursos de datos a través de la API de resto simple, que están disponibles para cualquier cliente capaz de enviar y recibir datos a través de HTTP/HTTPS.

Almacenamiento de Azure Premium brinda soporte de disco de alto rendimiento, baja latencia para i/OS cargas de trabajo intensivas ejecuta máquinas virtuales encendidas Azure. Con el almacenamiento de Azure Premium, puede adjuntar varios discos de datos persistente a una máquina virtual y configurarlos para cumplir los requisitos de rendimiento. Cada disco de datos está respaldado por un disco SSD en el almacenamiento de Azure Premium para obtener el máximo rendimiento de E/S. Consulte [Premium almacenamiento: almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](storage-premium-storage.md) para obtener más detalles.

## <a name="introducing-the-azure-storage-services"></a>Introducción a los servicios de almacenamiento de Azure

Almacenamiento de Azure proporciona los cuatro servicios siguientes: almacenamiento, almacenamiento de información de tabla, almacenamiento en cola y almacenamiento de archivos de objetos binarios.

- Almacenamiento BLOB almacena datos no estructurados de objeto. Un blob puede ser cualquier tipo de texto o datos binarios, como un documento, un archivo multimedia o un instalador de la aplicación. Almacenamiento de blobs también se conoce como almacenamiento de objetos.
- Almacenamiento de tabla almacena los conjuntos de datos estructurados. Almacenamiento de la tabla es un almacén de datos del atributo de clave de NoSQL, lo que permite el desarrollo rápido y rápido acceso a grandes cantidades de datos.
- Almacenamiento en cola proporciona mensajería confiable para el procesamiento de flujo de trabajo y para la comunicación entre los componentes de servicios de nube.
- Almacenamiento de archivos ofrece un almacenamiento compartido en aplicaciones que utilizan el protocolo SMB estándar. Azure máquinas virtuales y servicios en la nube pueden compartir datos de archivos a través de componentes de la aplicación a través de recursos compartidos montados y aplicaciones locales pueden tener acceso a los datos de archivo en un recurso compartido a través del servicio de archivo REST API.

Una cuenta de almacenamiento de Azure es una cuenta segura que le da acceso a los servicios de almacenamiento de Azure. La cuenta de almacenamiento proporciona el espacio de nombres único para sus recursos de almacenamiento. La imagen siguiente ilustra las relaciones entre los recursos de almacenamiento de Azure en una cuenta de almacenamiento:

![Recursos de almacenamiento de Azure](./media/storage-introduction/storage-concepts.png)

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

[AZURE.INCLUDE [storage-versions-include](../../includes/storage-versions-include.md)]

## <a name="blob-storage"></a>Almacenamiento de blobs

Para que usuarios con grandes cantidades de datos de objeto no estructurado almacenar en la nube, el almacenamiento de blobs ofrece una solución rentable y escalable. Puede utilizar el almacenamiento de blobs para almacenar el contenido como:

- Documentos
- Datos sociales, como blogs, vídeos, música y fotos
- Copias de seguridad de archivos, equipos, bases de datos y dispositivos
- Imágenes y texto para aplicaciones web
- Datos de configuración de aplicaciones de nube
- Datos grandes, como los registros y otros conjuntos de datos grandes

Cada blob se organiza en un contenedor. Contenedores también proporcionan una forma útil para asignar directivas de seguridad a grupos de objetos. Una cuenta de almacenamiento puede contener cualquier número de contenedores y un contenedor puede contener cualquier número de blobs, hasta el límite de capacidad de 500 TB de la cuenta de almacenamiento.  

BLOB ofrece tres tipos de almacenamiento de blobs, bloquear blobs, anexar BLOB y BLOB de página (discos).

- BLOB de bloque está optimizado para la transmisión y el almacenamiento de objetos de la nube y es una buena opción para almacenar documentos, archivos multimedia, copias de seguridad etcetera.
- Anexar blobs son similares a los blobs de bloque, pero están optimizados para la operación de anexión. Un blob de datos anexados se puede actualizar sólo agregando un nuevo bloque al final. Anexar los BLOB son una buena opción para escenarios como registro, donde deben escribirse al final del objeto binario de datos nuevos.
- Blobs de página están optimizados para que representan discos de IaaS y apoyando aleatorio escribe y puede ser de hasta 1 TB de tamaño. IaaS disco es un VHD almacenado como un blob de página conectado a una red de Azure máquina virtual.

Para conjuntos de datos muy grandes donde las restricciones de red Asegúrese de cargar o descargar datos para el almacenamiento de blobs por el cable poco realista, puede enviar una unidad de disco duro a Microsoft para importar o exportar datos directamente desde el centro de datos. Consulte [usar el servicio de importación/exportación de Microsoft Azure para transferir los datos para el almacenamiento de blobs](storage-import-export-service.md).

## <a name="table-storage"></a>Almacenamiento de la tabla

Las aplicaciones modernas exigen a menudo almacenes de datos con la mayor escalabilidad y flexibilidad que las generaciones anteriores de software requerido. Almacenamiento de tabla ofrece almacenamiento de información altamente disponible y escalable, de manera que la aplicación automáticamente puede escalar para satisfacer la demanda de los usuarios. Almacenamiento de la tabla es almacén clave o atributo de Microsoft – tiene un diseño schemaless, lo que es diferente de bases de datos relacionales tradicionales. Con un almacén de datos schemaless, es fácil adaptar los datos a medida que las necesidades de evolucionar de su aplicación. Almacenamiento de la tabla es fácil de usar, por lo que los desarrolladores pueden crear aplicaciones rápidamente. Acceso a datos es rápido y rentable para todo tipo de aplicaciones.  Almacenamiento de tabla suele ser significativamente menor coste que SQL tradicional similares volúmenes de datos.

Almacenamiento de la tabla es un almacén de atributo de clave, lo que significa que se almacena cada valor en una tabla con un nombre de propiedad con tipo. El nombre de la propiedad puede utilizarse para filtrar y especificar criterios de selección. Una colección de propiedades y sus valores constituyen una entidad. Puesto que el almacenamiento de información de tabla es schemaless, dos entidades en la misma tabla pueden contener diferentes colecciones de propiedades y esas propiedades pueden ser de diferentes tipos.

Puede utilizar almacenamiento de tabla para almacenar conjuntos de datos flexible, como los datos de usuario para aplicaciones web, libretas de direcciones, información de dispositivo y cualquier otro tipo de metadatos que el servicio requiere.  Puede almacenar cualquier número de entidades en una tabla y una cuenta de almacenamiento puede contener cualquier número de tablas, hasta el límite de capacidad de la cuenta de almacenamiento.

Como Blobs y colas, los desarrolladores pueden administrar y tener acceso a tabla protocolos de almacenamiento de información con el resto estándar, sin embargo, el almacenamiento de información de tabla también admite un subconjunto del Protocolo OData, simplificando las avanzadas capacidades de consulta y habilitación de JSON y AtomPub (basado en XML) formatos.

Para aplicaciones basadas en Internet de hoy en día, las bases de datos NoSQL como almacenamiento de tabla ofrecen una alternativa popular para bases de datos relacionales tradicionales.

## <a name="queue-storage"></a>Almacenamiento en cola

Al diseñar aplicaciones de escala, a menudo se separan componentes de la aplicación, por lo que puede escalar de manera independiente. Almacenamiento en cola proporciona una solución de mensajería confiable para la comunicación asincrónica entre los componentes de la aplicación, si éstas se ejecutan en la nube, en el escritorio, en un servidor local o en un dispositivo móvil. Almacenamiento de la cola también admite la administración de tareas asincrónicas y crear flujos de trabajo de proceso.

Una cuenta de almacenamiento puede contener cualquier número de colas. Una cola puede contener cualquier número de mensajes, hasta el límite de capacidad de la cuenta de almacenamiento. Mensajes individuales pueden ser hasta 64 KB de tamaño.

## <a name="file-storage"></a>Almacenamiento de archivos

Almacenamiento de Azure archivo ofrece basados en cloud SMB recursos compartidos de archivos, de manera que puede migrar aplicaciones heredadas que se basan en recursos compartidos de archivos Azure rápidamente y sin costosas reescribe. Con el almacenamiento de Azure archivos, aplicaciones que se ejecutan en máquinas virtuales Azure o servicios de nube pueden montar un recurso compartido de archivos en la nube, como una aplicación de escritorio monta un recurso compartido de SMB típica. Cualquier número de componentes de la aplicación puede, a continuación, montar y tener acceso simultáneamente a la cuota de almacenamiento del archivo.

Desde un recurso compartido de almacenamiento es un archivo compartido SMB estándar, aplicaciones que se ejecutan en Azure pueden tener acceso a los datos en el recurso compartido a través de las APIs de I/O del sistema de archivos. Por lo tanto, los desarrolladores pueden aprovechar su código existente y las habilidades para migrar las aplicaciones existentes. Los profesionales de TI pueden usar los cmdlets de PowerShell para crear, montar y administrar recursos compartidos de almacenamiento de archivos como parte de la administración de aplicaciones de Azure.

Como los servicios de almacenamiento de Azure, almacenamiento de archivos expone una API de REST para acceso a datos en un recurso compartido. Aplicaciones locales pueden llamar el almacenamiento de archivos REST API para tener acceso a datos en un recurso compartido de archivos. De este modo, una empresa puede elegir migrar algunas aplicaciones heredadas a Azure y seguir ejecutando otras personas dentro de su propia organización. Tenga en cuenta que sólo es posible para las aplicaciones que se ejecutan en Azure; montar un recurso compartido de archivos una aplicación local sólo puede tener acceso el recurso compartido de archivos a través de la API de REST.

Aplicaciones distribuidas también pueden utilizar el almacenamiento de archivos para almacenar y compartir los datos de aplicación útil, desarrollo y herramientas de prueba. Por ejemplo, una aplicación puede almacenar archivos de configuración y datos de diagnóstico, como registros, métricas y bloqueo se vuelca en un archivo de almacenamiento compartir para que estén disponibles para múltiples máquinas virtuales o funciones. Los desarrolladores y los administradores pueden almacenar utilidades que necesitan para crear o administrar una aplicación en un recurso compartido de almacenamiento de información de archivo que está disponible para todos los componentes, en lugar de instalarlos en cada máquina virtual o la instancia del rol.

## <a name="access-to-blob-table-queue-and-file-resources"></a>Acceso a recursos de archivo, tabla, cola y Blob

De forma predeterminada, sólo el propietario de la cuenta de almacenamiento puede tener acceso a los recursos en la cuenta de almacenamiento. Para la seguridad de los datos, se debe autenticar cada solicitud realizada contra los recursos en su cuenta. La autenticación se basa en un modelo de clave compartida. BLOB también pueden configurarse para admitir la autenticación anónima.

La cuenta de almacenamiento se asigna a dos teclas de acceso privado de creación que se utilizan para la autenticación. Tener dos claves garantiza que la aplicación sigue estando disponible cuando se regeneran con regularidad las claves como una práctica común de administración de claves de seguridad.

Si es necesario permitir a los usuarios el acceso controlado a los recursos de almacenamiento de información, puede crear una firma de acceso compartido. Una firma de acceso compartido (SAS) es un símbolo (token) que se puede anexar a una dirección URL que permite el acceso delegado a un recurso de almacenamiento de información. Cualquier persona que posee el token puede acceder al recurso que señala con los permisos que se especifica, para el período de tiempo que sea válido. A partir de la versión 2015-04-05, almacenamiento Azure admite dos tipos de firmas de acceso compartido: SAS de servicios y la cuenta SA.

El servicio SAS delega el acceso a un recurso en sólo uno de los servicios de almacenamiento de información: el servicio de Blob, colas, tabla o archivo.

Una cuenta SA delega el acceso a los recursos de uno o más de los servicios de almacenamiento de información. Puede delegar el acceso a las operaciones de nivel de servicio que no están disponibles con un servicio de SAS. También puede delegar el acceso para leer, escribir y eliminar operaciones en contenedores de blob, tablas, colas y recursos compartidos de archivos que no se permiten con un servicio de SAS.

Por último, puede especificar que un contenedor y sus blobs o un blob específico, están disponibles para el acceso público. Al indicar que un contenedor o un blob es público, cualquiera puede leer de forma anónima; no se requiere autenticación.  BLOB y contenedores públicos es útil para exponer los recursos como los medios y documentos que están alojados en sitios Web.  Para reducir la latencia de la red para una audiencia global, puede almacenar en caché datos blob utilizados por sitios Web con la CDN de Azure.

Consulte [Uso compartido acceso firmas (SAS)](storage-dotnet-shared-access-signature-part-1.md) para obtener más información sobre las firmas de acceso compartido. Para obtener más información sobre el acceso seguro a su cuenta de almacenamiento, consulte [Administrar acceso de lectura anónimo a contenedores y objetos BLOB](storage-manage-access-to-resources.md) y [autenticación para los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx) .

## <a name="replication-for-durability-and-high-availability"></a>Replicación de durabilidad y alta disponibilidad

Siempre se replican los datos de su cuenta de almacenamiento de información de Microsoft Azure para garantizar la durabilidad y alta disponibilidad. La replicación copia los datos, en el mismo data center o en un segundo centro de datos, dependiendo de la opción de replicación que elija. Replicación protege sus datos y conserva el tiempo de su aplicación en caso de fallas de hardware transitorios. Si los datos se replican en un segundo centro de datos, también protege sus datos contra una falla catastrófica en la ubicación principal.

La replicación garantiza que su cuenta de almacenamiento cumple con el [Acuerdo de nivel de servicio (SLA) para almacenamiento de información](https://azure.microsoft.com/support/legal/sla/storage/) incluso frente a errores. Consulte que el SLA para obtener información acerca del almacenamiento de Azure garantías de durabilidad y disponibilidad. 

Cuando se crea una cuenta de almacenamiento, puede seleccionar una de las siguientes opciones de replicación:  

- **Almacenamiento redundante local (LRS).** Almacenamiento redundante local mantiene tres copias de los datos. LRS se replica tres veces dentro de un centro de datos único de una única región. LRS protege sus datos de fallas de hardware normal, pero no de la falla de un solo data center.  

    LRS se ofrece a un precio reducido. Para una máxima durabilidad, recomendamos que utilice almacenamiento geo redundantes, que se describe a continuación.


- **Almacenamiento de información redundantes de zona (ZRS).** Almacenamiento redundante de zona mantiene tres copias de los datos. ZRS se replican tres veces a través de dos o tres instalaciones, dentro de una única región o a través de dos regiones, proporcionando mayor durabilidad que LRS. ZRS garantiza que sus datos son duraderos dentro de una única región.  

    ZRS proporciona un mayor nivel de durabilidad que LRS; Sin embargo, para una máxima durabilidad, recomendamos que utilice almacenamiento geo redundantes, que se describe a continuación.  

    > [AZURE.NOTE] ZRS está disponible sólo para blobs de bloque y sólo se admite para las versiones de 2014-02-14 y versiones posteriores.
    >
    > Una vez que se ha creado la cuenta de almacenamiento y seleccionado ZRS, no se puede convertir se utiliza para cualquier otro tipo de duplicación, o viceversa.

- **Almacenamiento redundante Geo (GRS)**. GRS mantiene seis copias de sus datos. Con GRS, los datos se replican tres veces dentro de la región primaria y también se replican tres veces en una zona secundaria cientos de millas lejos de la región principal, ofrecer el más alto nivel de durabilidad. En el caso de una falla en la región principal, almacenamiento Azure hará failover en la zona secundaria. GRS garantiza que sus datos están duraderos en dos regiones separadas.

    Para obtener información acerca de emparejamientos primaria y secundaria por región, vea [Áreas de Azure](https://azure.microsoft.com/regions/).

- **Acceso de lectura, almacenamiento redundante geo (RA GRS)**. Almacenamiento redundante geo acceso de lectura replica los datos a una ubicación geográfica secundaria y también proporciona acceso de lectura a los datos en la ubicación secundaria. Almacenamiento redundante geo acceso de lectura permite tener acceso a los datos desde el primario o en la ubicación secundaria, en caso de que una ubicación no está disponible. Almacenamiento redundante geo acceso de lectura es la opción predeterminada para la cuenta de almacenamiento predeterminada al crearla. 

    > [AZURE.IMPORTANT] Puede cambiar cómo los datos se replican después de que se ha creado la cuenta de almacenamiento, a menos que especifique ZRS cuando se creó la cuenta. Sin embargo, tenga en cuenta que puede incurrir a una transferencia de datos adicionales de una sola vez si se cambia de LRS a GRS o RA GRS de costo.

Para obtener más información acerca de las opciones de replicación de almacenamiento de información, vea [replicación de almacenamiento de Azure](storage-redundancy.md) .

Para información sobre precios para replicación de la cuenta de almacenamiento, consulte [Precios de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/). Vea [Áreas de Azure](https://azure.microsoft.com/regions/#services) para obtener más información acerca de qué servicios están disponibles en cada región.

Para obtener detalles arquitectónicos sobre durabilidad con el almacenamiento de Azure, consulte [SOSP Paper - almacenamiento de Azure: altamente disponible nube almacenamiento servicio con consistencia de seguro](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).


## <a name="transferring-data-to-and-from-azure-storage"></a>Transferir datos desde y hacia el almacenamiento de Azure

Puede utilizar la utilidad de línea de comandos AzCopy para copiar blob, archivos y datos de la tabla dentro de su cuenta de almacenamiento de información o entre cuentas de almacenamiento. Para obtener más información, consulte [transferencia de datos con la utilidad de línea de comandos para AzCopy](storage-use-azcopy.md) .

AzCopy se basa en la [Biblioteca de movimiento de datos de Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), que actualmente está disponible en vista previa.

El servicio de importación y exportación de Azure proporciona una manera de importar los datos blob o exportar datos blob desde su cuenta de almacenamiento de información a través de un disco de la unidad de disco duro por correo al centro de datos de Azure. Para obtener más información acerca del servicio de importación o exportación, consulte [usar el servicio de importación/exportación de Microsoft Azure para transferir datos al almacenamiento de blobs](storage-import-export-service.md).

## <a name="pricing"></a>Precios

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

## <a name="storage-apis-libraries-and-tools"></a>APIs de almacenamiento de información, bibliotecas y herramientas

Recursos de almacenamiento de Azure pueden tener acceso mediante cualquier lenguaje que puede realizar solicitudes HTTP/HTTPS. Además, el almacenamiento de Azure ofrece bibliotecas de programación para varios lenguajes populares. Estas bibliotecas simplifican muchos aspectos del trabajo con el almacenamiento de Azure por controlar detalles como invocaciones sincrónicas y asincrónicas, procesamiento por lotes de las operaciones, administración de excepciones, reintentos automáticos, comportamiento operativo y así sucesivamente. Bibliotecas están actualmente disponibles para los siguientes lenguajes y plataformas, con otras personas en la canalización:

### <a name="azure-storage-data-services"></a>Servicios de datos de almacenamiento de Azure

- [REST API de servicios de almacenamiento de información](http://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Biblioteca de cliente de almacenamiento de información para. NET, Windows Phone y en tiempo de ejecución de Windows](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Biblioteca de cliente de almacenamiento de información para C++](https://github.com/Azure/azure-storage-cpp)
- [Biblioteca de cliente de almacenamiento de información para Java/Android](/develop/java/)
- [Biblioteca de cliente de almacenamiento de información para Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
- [Biblioteca de cliente de almacenamiento de información para PHP](/develop/php/)
- [Biblioteca de cliente de almacenamiento de información para Ruby](/develop/ruby/)
- [Biblioteca de cliente de almacenamiento de información para Python](/develop/python/)
- [Almacenamiento Cmdlets para PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt269418.aspx)

### <a name="azure-storage-management-services"></a>Servicios de administración de almacenamiento de Azure

- [Referencia de la API de almacenamiento recurso proveedor resto](https://msdn.microsoft.com/library/azure/mt163683.aspx)
- [Biblioteca de cliente de proveedor de recursos de almacenamiento para .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx)
- [Cmdlets de proveedor de recursos de almacenamiento para PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt607151.aspx)
- [Almacenamiento servicio Administración REST API (estándar)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-services"></a>Servicios de movimiento de datos de almacenamiento de Azure

- [Servicio REST API de importación/exportación de almacenamiento](https://msdn.microsoft.com/library/azure/dn529096.aspx)
- [Biblioteca de cliente de movimiento de datos de almacenamiento para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### <a name="tools-and-utilities"></a>Herramientas y utilidades

- [Explorador de almacenamiento de Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
- [Herramientas de cliente de almacenamiento de Azure](storage-explorers.md)
- [Herramientas y SDK de azure](https://azure.microsoft.com/tools/)
- [Emulador de almacenamiento de Azure](http://www.microsoft.com/download/details.aspx?id=43709)
- [PowerShell de Azure](../powershell-install-configure.md)
- [Utilidad de línea de comandos AzCopy](http://aka.ms/downloadazcopy)

## <a name="next-steps"></a>Próximos pasos

Para obtener más información sobre el almacenamiento de Azure, examine estos recursos:

### <a name="documentation"></a>Documentación

- [Documentación del almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)

### <a name="for-administrators"></a>Para los administradores

- [Usando PowerShell Azure con almacenamiento de Azure](storage-powershell-guide-full.md)
- [Uso de Azure CLI con almacenamiento de Azure](storage-azure-cli.md)

### <a name="for-net-developers"></a>Para los desarrolladores de .NET

- [Introducción a almacenamiento Azure Blob mediante .NET](storage-dotnet-how-to-use-blobs.md)
- [Empezar a trabajar con el almacenamiento de Azure tabla mediante .NET](storage-dotnet-how-to-use-tables.md)
- [Empezar a trabajar con el almacenamiento de Azure cola mediante .NET](storage-dotnet-how-to-use-queues.md)
- [Empezar a trabajar con el almacenamiento de archivos de Azure en Windows](storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>Para los desarrolladores de Java/Android

- [Cómo utilizar el almacenamiento de blobs de Java](storage-java-how-to-use-blob-storage.md)
- [Cómo utilizar el almacenamiento de información de tabla de Java](storage-java-how-to-use-table-storage.md)
- [Cómo utilizar el almacenamiento en cola de Java](storage-java-how-to-use-queue-storage.md)
- [Cómo utilizar el almacenamiento de archivos de Java](storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>Para los desarrolladores de Node.js

- [Cómo utilizar el almacenamiento de blobs de Node.js](storage-nodejs-how-to-use-blob-storage.md)
- [Cómo utilizar el almacenamiento de información de tabla de Node.js](storage-nodejs-how-to-use-table-storage.md)
- [Cómo utilizar el almacenamiento en cola de Node.js](storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Para desarrolladores de PHP

- [Cómo utilizar el almacenamiento de blobs de PHP](storage-php-how-to-use-blobs.md)
- [Cómo utilizar el almacenamiento de información de tabla de PHP](storage-php-how-to-use-table-storage.md)
- [Cómo utilizar el almacenamiento en cola de PHP](storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Para los desarrolladores de rubí

- [Cómo utilizar el almacenamiento de blobs de rubí](storage-ruby-how-to-use-blob-storage.md)
- [Cómo utilizar el almacenamiento de información de tabla de rubí](storage-ruby-how-to-use-table-storage.md)
- [Cómo utilizar el almacenamiento en cola de rubí](storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Para los programadores de Python

- [Cómo utilizar el almacenamiento de blobs de Python](storage-python-how-to-use-blob-storage.md)
- [Cómo utilizar el almacenamiento de información de tabla de Python](storage-python-how-to-use-table-storage.md)
- [Cómo utilizar el almacenamiento en cola de Python](storage-python-how-to-use-queue-storage.md)
- [Cómo utilizar el almacenamiento de archivos de Python](storage-python-how-to-use-file-storage.md)
