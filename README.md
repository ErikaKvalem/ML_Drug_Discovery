# ML_Drug_Discovery
PROYECTO DE BIOINFORMÁTICA – DESARROLLO COMPUTACIONAL DE FÁRMACOS

Para el desarrollo de este proyecto me baso en la relación cuantitativa estructura-actividad (Quantitative structure-activity relationship, QSAR). Esto quiere decir que la estructura  química de los compuestos se correlaciona cuantitativamente con la actividad biológica. La actividad biológica es define como la unión de un fármaco con un receptor ( proteína, organismo …)   Permite entender el origen biológico de la actividad e interpretar los modelos QSAR tiene un gran valor e importancia en el desarrollo y diseño de nuevos fármacos

SELECCIONAR LOS DATOS Y PRE-PROCESADO

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





ANÁLISIS EXPLORATORIO DE LOS DATOS

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

Interpretación de los resultados estadísticos

•	pIC50 values

Mirando los valores de pIC50, las clases active y inactive muestran diferencia estadística significativa, lo cual era de esperar ya que habíamos fijado los valores de umbral previamente para crear las clases. ( IC50 < 1000 nM = Active, IC50 > 10000 nM = Inactive, corresponde a pIC50 > 6 Actives , and pIC50< 5 = Inactives)
•	Descriptores Lipinski

De los 4 descriptores (Lipinski's descriptors,MW, LogP, NumHDonors, NumHAcceptors) sólo LogP no muestra diferencia estadística significativa entre clases. Los otros 3 descriptores todos mostraron diferencia estadística significativa entre las clases de bioactividad "active" y "inative"

CÁLCULO DE LOS DESCRIPTORES PUBCHEM FINGERPRINTY PREPARACIÓN DE LOS DATOS PARA ML

En esta parte he calculado unos descriptores que describen las características locales de las moléculas que usaré para construir los modelos de ML. Estos descriptores se llaman descriptores PubChem Fingerprint que describen cuantitativamente los compuestos del dataset. 

Estos son los pasos que he seguido: 

1.Descargar el software PaDEL y aplicarlo a los datos. Genera un resultado descriptors_output.csv.

El software PaDEL usa java y un fichero PaDEL_descriptor.jar que ejecuto. Se encarga de limpiar los valores de la variable canonical_smiles que contiene información de la estructura de los compuestos. Elimina las sales Cloro y Socio (Cl, Na) y elimina también los ácidos orgánicos para que no haya impurezas.

2. Separo los datos en matriz X e Y (target). Elimino el nombre de los compuesto (Name) de la matriz X . Esta matrix contiene los descriptores PubChem Fingerprint. La Y contiene el valor pIC50 de bioactividad. 

CONSTRUIR MODELO SIMPLE DE REGRESIÓN RANDOM FOREST

Con la X e Y creadas anteriormente pruebo a construir un modelo de regresión de Random Forest. 
Estos son los pasos que he seguido: 
1. Elimino las variables con varianza baja.
2. Divido los datos en train (80%) y test (20%) 
3. Creo un modelo con el siguiente código y obtengo un valor de 0.47 para R^2. 
  
MODELOS DE REGRESIÓN MACHINE LEARNING

En esta sección voy a construir modelos de ML para los datos de bioactividad para la proteína  Human Acetylcholinesterase basándome en los predictores PubChem Fingerprint. Estos predictores son huellas únicas para cada compuesto que los identifican y les confieren propiedades únicas. Esto es esencial para el desarrollo de fármacos y su diseño.

 La conectividad entre las moléculas da lugar a estructuras únicas del compuesto y también propiedades. Necesitamos encontrar una forma de reajustar dichas moléculas de tal manera que el compuesto final, el fármaco sea lo más potente posible a la vez que lo menos tóxico y más seguro para el consumo. 

El algoritmo de ML aprende de los descriptores PubChem Fingerprint, de las propiedades moleculares únicas de cada compuesto. El objetivo es crear un modelo que sepa distinguir correctamente entre compuesto ACTIVO/INACTIVOS. Así podremos ver que grupos funcionales son esenciales para diseñar un fármaco potente y seguro. Es por eso que la variable dependiente, Y (target) será el valor pIC50 con el que hemos estado trabajando. Este es el valor de la concentración de compuesto necesario para la inhibición del 50% de la proteína. 

Para este propósito he usado la librería Lazypredict que me permite construir 39 algoritmos básicos presentes en scikit-learn simulatenamente. Es importante decir que son entrenado con los parámetro por defecto y que se podría mejorar su rendimiento aplicando optimización de hyperparámetros con GridSearch por ejemplo. 

Conclusiones

El rendimiento de los 39 modelos no es para nada bueno. El mejor predictor es el modelo LGBMRegressor el cual alcanza un valor de R2 de 0.4. Esto puede deberse a que al aplicar Lazypredict estamos usando los parámetros que están establecidos por defecto para los algoritmos. 

Mi estrategia sería aplicar un GridSearch individualmente a los 5 primeros modelos para ver si la predicción mejora. En segundo lugar intentaría encontrar una proteína con mayor cantidad de compuestos (+ 5000 registros) y haría un análisis más exhaustivo de las variables y su importancia. 

Este ha sido un proyecto interesante del que he aprendido mucho aunque creo que la parte biológica  y del EDA me ha ocupado 80% del tiempo del trabajo dejando 20% de dedicación a la parte de ML la cual podría ser mejorada en muchos aspectos. 

Aprendizajes: 
-	Trabajar en Google Colab
-	Base de datos ChEMBL
-	Instalación de rdkit 
-	Descriptores Lipinski
-	IC50/pIC50
-	Man Whitney test estadístico
-	PubChem fingerprint descriptores 
-	Software PaDEL
-	LazyPredict librería

Problemas que he tenido que resolver: 
-	Incompatibilidad de librerías (ej: scikit-learn y LazyPredict)
-	Transformación de object a string y a float
-	Funciones que he usado de internet como Lipinski he tenido que modificarla para que funcoine con mi código
 






