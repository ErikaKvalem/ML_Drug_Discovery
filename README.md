# ML_Drug_Discovery
PROYECTO DE BIOINFORMÁTICA – DESARROLLO COMPUTACIONAL DE FÁRMACOS

Para el desarrollo de este proyecto me baso en la relación cuantitativa estructura-actividad (Quantitative structure-activity relationship, QSAR). Esto quiere decir que la estructura  química de los compuestos se correlaciona cuantitativamente con la actividad biológica. La actividad biológica es define como la unión de un fármaco con un receptor ( proteína, organismo …)   Permite entender el origen biológico de la actividad e interpretar los modelos QSAR tiene un gran valor e importancia en el desarrollo y diseño de nuevos fármacos

1.SELECCIONAR LOS DATOS Y PRE-PROCESADO

•	Base de datos ChEMBL

En este caso uso la base de datos ChEMBL de la qu descargamos los datos de bioactividad de los compuestos una vez seleccionada la proteína objetivo Human  Acetylcholinesterase. 

De la base de datos obtengo los compuestos (fármacos)  que tienen un cierto tipo de efecto en la proteína/organismo objetivo. Este efecto puede ser de inhibición o de aceleración de la actividad de la proteína en este caso Human  Acetylcholinesterase. 

Estos son los pasos que he seguido:

1.	Instalar el cliente de CheMBL 
2.	Importar las librerías
3.	Buscar los resultados disponibles para la proteína objetivo (Filtro: single protein)
4.	Buscar los datos de bioactividad para dicha proteína  (standard_type = "IC50")
Es importante mencionar que selecciono el tipo stanard IC5 para que mi dataset tenga unidades uniformes de bioactividad (nM)
5.	He salvado los datos en un fichero bioactivity_data.csv

•	Pre-procesado de los datos de bioactividad

El valor de la bioactividad lo encuentro en la columna standard_value. Este valor indica la potencia del fármaco. Indica la concentración de fármaco necesaria para la inhibición del 50% de la proteína. A menor valor, menor cantidad de fármaco necesitamos para crear el mismo efecto por ello, mayor potencia. Idealmente deseamos encontrar el menor valor posible.  

Estos son los pasos que he seguido:

1.	Si alguno de los compuestos tiene valores nulos en la columna standard_value, lo elimino
2.	Etiqueto los compuestos como activo, intermedio e inativo. 
Los datos de bioactividad son valores IC50 (Concentración para la inhibición de 50%) . Los compuestos con valores inferiores a 1000 nM se consideran ACTIVOS. Los que tengan valores superiores a 10000 nM se consideran INACTIVOS. Los compuestos entre ambos valores (mayores que 1000 e inferiores a 10000) son INTERMEDIOS.

3.	Combino las 3 columnas molecue_chembl_id, canonical_smiles y standard_value  después de haber eliminado las filas duplicadas en un DataFrame y salvo esa información en bioactivity_preprocessed_data.csv


![image](https://user-images.githubusercontent.com/39902241/127727051-0edd81c7-9ea1-4fc9-92a4-76fc7cb2fe10.png)

