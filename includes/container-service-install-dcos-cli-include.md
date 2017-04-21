<properties
   pageTitle="Instalar la CLI de DC/OS | Microsoft Azure"
   description="Instale el controlador de dominio/OS CLI."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contenedores, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="rogardle"/>

>[AZURE.NOTE] Se trata de trabajar con clústeres basados en DC/OS ACS. No hay ninguna necesidad de hacerlo para clústeres basados en enjambre ACS.

En primer lugar, [conectarse al clúster basados en DC/OS ACS](../articles/container-service/container-service-connect.md). Una vez haya hecho esto, puede instalar la CLI de DC/OS en el equipo cliente con los siguientes comandos:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Si está utilizando una versión antigua de Python, puede observar algunas "InsecurePlatformWarnings". Puede ignorar estos.

Para poder empezar sin tener que reiniciar el shell, ejecute:

```bash
source ~/.bashrc
```

Este paso no será necesario cuando inicia shells de nuevo.

Ahora puede confirmar que está instalado el CLI:

```bash
dcos --help
```