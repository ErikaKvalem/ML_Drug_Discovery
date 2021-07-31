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





2. ANÁLISIS EXPLORATORIO DE LOS DATOS

En esta sección calculo los descriptores  de Lipinski y hago un análisis exploratorio de los datos.

•	Descriptores de Lipinski

Estos descriptores dan una visión global de las propiedades de las moléculas para saber si pueden ser una buen fármaco para via oral. Christopher Lipinski, es un científico en Pfizer, que estableció una serie de reglas para evaluar la efectividad de un fármaco. Esta efectividad sebasa en el perfil farmacokinetico también conocido como ADME por sus siglas en inglés (Absorption, Distribution, Metabolism, & Extraction). Este científico analizó todos los fármacos activos de via oral aprovado por la FDA americana y estabeció lo que se conoce como the Rule-of-Five of Lipinski's Rule.

The Lipinski's Rule dice lo siguiente:
•	El peso molecular (MW) <500 Dalton
•	EL coefficiente de particion Octanol-agua (LogP)<5
•	Enlaces de hidrogeno donantes <5
•	Enlaces de hidrógeno aceptadores <10


Estos son los pasos que he seguido: 
1.	Instalo conda en Google Colab e importo la librería rdkit, librería de software libre para química-informática. 
2.	Calculo los descriptores de Lipinski usando una función modificada de Codeocean

Los descriptores de Lipinski son el peso molecular (Molecular Weight, MW), la solubilidad (LogP) el número de enlaces de hidrogeno cedidos (NumHDonors) y el número de enlaces de hidrógeno acceptados (NumHAcceptors).

3.	Combino los dataframes de la bioactividad con el resultado de los descriptores de Lipinski.
4.	Fijo los valores de IC50 a un máximo de 100000000 para la posterior transformación usando la función norm_value


5.	Convierto el valor IC50 en pIC50 aplicando una transformación logarítmica negativa para lograr una distribución de los datos más uniforme. Uso la función pIC50
6.	Elimino la clase “intermediate” y me quedo sólo con las clases de compuestos activos e inactivo que había definido previamente.
7.	Guardo los datos en final_df_ipc50.csv

•	Análisis exploratorio del espacio químico basándome en los descriptores de Lipinski calculados. 

Uso la función estadística Mann-Whitney U Test función modificada de Machine Learning Mastery
![image](https://user-images.githubusercontent.com/39902241/127727077-df53b0c0-1e3b-400e-93c3-a78808ce8f31.png)


