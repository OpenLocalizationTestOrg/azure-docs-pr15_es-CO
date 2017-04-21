## <a name="defining-a-backup-policy"></a>Definición de una directiva de copia de seguridad

Una directiva de copia de seguridad define una matriz de cuando se toman las instantáneas de los datos y cuánto tiempo se mantienen esas instantáneas. Al definir una directiva para crear copias de una máquina virtual, puede desencadenar un trabajo de copia de seguridad *una vez al día*. Cuando se crea una nueva directiva, se aplica a la caja fuerte. La interfaz de directiva de copia de seguridad tiene el siguiente aspecto:

![Política de backup](./media/backup-create-policy-for-vms/backup-policy.png)

Para crear una directiva:

1. Escriba un nombre para el **nombre de la directiva**.

2. Pueden tomar instantáneas de los datos a intervalos diarios o semanales. Utilice el menú desplegable **Frecuencia de copia de seguridad** para elegir si las instantáneas de datos se realizan diariamente o semanalmente.

    - Si elige un intervalo diario, utilice el control resaltado para seleccionar la hora del día para la instantánea. Para cambiar la hora, anule la selección de la hora y seleccione la nueva hora.

    ![Directiva de copia de seguridad diaria](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>

    - Si elige un intervalo semanal, utilice los controles resaltados para seleccionar los días de la semana y la hora del día para tomar la instantánea. En el menú del día, seleccione uno o varios días. En el menú hora, seleccione una hora. Para cambiar la hora, anule la selección de la hora seleccionada y seleccione la nueva hora.

    ![Directiva de copia de seguridad semanal](./media/backup-create-policy-for-vms/backup-policy-weekly.png)

3. De forma predeterminada, se seleccionan todas las opciones de **Intervalo de retención** . Desactive cualquier límite de intervalo de retención que no desea utilizar. A continuación, especifique el intervalo a utilizar.

    Mensual y anual de los intervalos de retención permiten especificar las instantáneas basadas en un incremento diario o semanal.

    >[AZURE.NOTE] Cuando se protege una máquina virtual, ejecuta un trabajo de copia de seguridad una vez al día. El tiempo cuando se ejecuta la copia de seguridad es el mismo para cada intervalo de retención.

4. Después de configurar todas las opciones de la directiva, en la parte superior de la hoja, haga clic en **Guardar**.

    La nueva directiva se aplica inmediatamente a la caja fuerte.
