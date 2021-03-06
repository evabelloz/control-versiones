# control-versiones
---
 "Práctica R"
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

# Problemas a entregar sobre el Tema 2

-------------------------------------------------------------------------------------------------------------------

**Problema 1.-** Considerar el conjunto de datos *trees* de R que contiene la circunferencia, altura y volumen de cerezos.   

Para poder acceder al conjunto de datos, utilizamos la función ```data()```, le cambiamos los nombres a las variables y 
usamos ```attach()``` para no tener que llamar en cada paso al data frame *árboles*. Para comprobar si los datos se han cargado bien utilizamos la función ```head()``` que devuelve las seis primeras observaciones. 

```{r}
data("trees")
arboles<-trees
colnames(arboles)<-c("Circunferencia","Altura", "Volumen")
head(arboles)
attach(arboles) 

```

1.- Dividir las alturas en tres grupos y realizar un histograma de las restantes variables en cada grupo definido.

Hay muchas funciones con las que se puede dividir una variable determinada (o más) por grupos. En este caso, hemos elegido  ``` cut() ``` para dividir la variable *altura* y, posteriormente, se han definido tres datos diferentes correspondientes a cada uno de los grupos de la altura. 
```{r}
Grupo_Altura<-cut(Altura,3, labels = c(1,2,3))
arboles<-cbind(arboles,Grupo_Altura)

arboles_1 <- arboles[Grupo_Altura==1,] 
arboles_2 <- arboles[Grupo_Altura==2,]
arboles_3 <- arboles[Grupo_Altura==3,]
```


Como se comentado, *arboles_1* sólo contiene a los datos tales que su altura está en el intervalo (63, 71], *arboles_2* recoge los que están en (71,79] y, por último, *arboles_3* son los datos tales que su altura pertenece a (79, 87]. 
Una vez tenemos los datos dividos como nos interesa, ```hist()```, realizamos los gráficos que se piden. 

```{r}
par(mfrow=c(2,3))
hist(arboles_1$Circunferencia, col="yellow",main= "Histograma del primer grupo",
     xlab = "Circunferencia", ylab="Frecuencia")
hist(arboles_2$Circunferencia, col="purple",main= "Histograma del segundo grupo",
     xlab = "Circunferencia", ylab="Frecuencia")
hist(arboles_3$Circunferencia, col="pink",main= "Histograma del tercer grupo",
     xlab = "Circunferencia", ylab="Frecuencia")

hist(arboles_1$Volumen,col="yellow",main= "Histograma del primer grupo",
     xlab = "Volumen", ylab="Frecuencia")
hist(arboles_2$Volumen,  col="purple",main= "Histograma del segundo grupo",
     xlab = "Volumen", ylab="Frecuencia")
hist(arboles_3$Volumen, col="pink",main= "Histograma del tercer grupo",
     xlab = "Volumen", ylab="Frecuencia")
```

2.- Realizar un diagrama de dispersión de la circunferencia frente al volumen diferenciando los grupos definidos por la altura. Usar la librería ggplot2.  

Lo primero que hacemos es usar el comando ```library(ggplot2)```para poder utilizar las funciones del paquete que se nos pide. En caso de que sea la primera vez que lo utilizamos, probablemente debamos instalar el paquete con la ayuda de la función ```install.packages()```. Una vez hayamos hecho esos pasos, podremos utilizar el comando que hemos seleccionado para representar el gráfico de dispersión, ```qplot()```. Además de esta orden, se han usado otras para modificar el color de los puntos según grupos, la legenda y los títulos de la gráfica.


```{r fig.height=3, fig.width=5, message=FALSE, warning=FALSE}
library(ggplot2)

disp<-qplot(Circunferencia,Volumen,data=arboles,col=Grupo_Altura)

disp + 
scale_color_manual(labels = c("(63,71]", "(71,79]","(79,87]"),
                  values = c("cyan", "purple","orange")) +
labs(title = "Diagrama de dispersión", x = "Circunferencia (inch)",
     y = "Volumen (cubic ft)", color = "Grupos según \n       Altura")+
theme_bw()+theme(plot.title=element_text(size=14, hjust =0.5))
```


3.- Realizar un gráfico de dispersión y una regresión para cada par de variables y valorar la relación existente entre ellos.

Para realizar el gráfico tenemos dos formas de hacerlo, una de ellas sería usar ```plot()```para cada dos variables, y la otra, más rápida aunque menos recomendable si el números de variables es elevado, es el comando ```pairs()```. Para nuestro caso, al ser pocas variables, usamos la segunda opción eliminando la última columna que se corresponde con el número de grupo de cada árbol. 

```{r fig.height=4, fig.width=5, message=FALSE, warning=FALSE}
pairs(arboles[,-4])
```


Una vez se ha observado la gráfica, se puede extraer que algunos pares de variables guardan más correlación que otras. Con lo que cuando realicemos el modelo lineal, algunos R2 serán bastante más elevados que otros. 

Comenzamos haciendo y representando el modelo de regresión lineal simple para las variables *altura* y *circunferencia*. 

```{r include=FALSE}
par(mfrow=c(1,1))
```

```{r fig.height=4.1}
regre1<-lm(Altura~Circunferencia)
summary(regre1)
plot(Circunferencia,Altura)
abline(regre1,col=2,lwd=2)
```

Como se puede observar tanto en la gráfica como en el valor del R2, la *altura* no se puede explicar con precisión a partir de la variable *circunferencia*.

Realizamos el mismo proceso para las variables *altura* y *volumen*.

```{r}
regre2<-lm(Altura~Volumen)
plot(Volumen,Altura)
abline(regre2,col=2,lwd=3)
```

En este modelo el $R^2$ es $0.3579$, lo que implica que la relación que guardan estas dos variables es mayor que la que guardaban las anteriores, pero sigue sin ser muy buena.

Por último, seguimos el mismo proceso para las variables *circunferencia* y *volumen*. 

En este caso, se puede extraer de la gráfica que la relación es muy buena, el R2 es de 0.9353, lo que implica que la variable *volumen* es se puede explicar según el modelo *regre3* a un 93.53%. Además, tenemos que el modelo definido es,


Volumen=-36.9435 + 5.0659 * Circunferencia}.

Y para obtener su representación,  

```{r}
regre3<-lm(Volumen~Circunferencia)
plot(Circunferencia,Volumen)
abline(regre3,col=2,lwd=3)
```

Podemos establecer, basándonos en las gráficas de dispersión y en los modelos de regresión, que:
la variable Volumen se puede explicar muy bien (R2=0.9353) mediante la varible Circunferencia, 
o viceversa. Además, se puede observar que las variables Circunferencia y Altura están más 
incorreladas que las variables Altura y Volumen, aunque el modelo creado por estos dos últimas
sigue sin ser bueno.


**Problema 2.-** Seleccionar un fichero de datos adecuado en R para aplicar
cada uno de los siguientes análisis:

Para elegir unos datos correctos, vemos los diferentes ficheros que nos ofrece R utilizando la función 
```?dataset``` y, una vez que lo elijamos, lo llamamos mediante la función ```data()```. En este caso se ha
elegido *"Salaries"* que pertenece a la librería *carData*.

```{r}
library(carData)
data("Salaries")
```

Este conjunto de datos trata los salarios de los proferores en el año 2008-2009. Tiene 6 variables, que indican, respectivamente, el rango del profesor (factor de $3$ niveles)
la disciplina (dos factores A (departamento teórico), B (departamento aplicado)), los años desde 
el doctorado, los años de servicio, el sexo de la persona y el salario de 9 meses en dolares.

Le cambiamos los nombres a las variables para facilitar su manejo, 

```{r}
salarios<-Salaries
colnames(salarios)<-c("rango","disciplina","doctorado","servicio","sexo","salario")
```


1.- Calcular la tabla de frecuencias absolutas de al menos dos variables.

Para calcular la tabla de frecuencias absolutas se usa la función ```table()``` y si queremos obtenerla en formato latex debemos usar la libraría *knitr*. 

Utilizando lo mencionado, calculamos la tabla de frecuencias absolutas de la variable *rango*.
```{r}
attach(salarios)
FrecAbs_rango<-table(rango)
FrecAbs_rango
```


```{r include=FALSE}
attach(salarios)
FrecAbs_rango<-table(rango)
library(knitr)
print(knitr::kable(as.data.frame(FrecAbs_rango),  format = "latex",
                   col.names =c("Rango del profesor","Num. Profesores"),align='c'))
```


De la misma manera, calculamos la tabla de frecuencias absolutas de la variable *servicio*. Para ello, como esta variable es continua, primero la partimos en diferentes intervalos mediante ```cut()```. 

```{r}
intervalos_servicio <- cut(servicio,breaks = c(0,10,20,30,40,50,60), include.lowest = T)
FrecAbs_servicio<-table(intervalos_servicio)
FrecAbs_servicio
```

Y se realiza lo mismo con la variable *salario*. 

2.- Calcular la tabla de frecuencias (absolutas, relativas y acumuladas) formateada
para latex para al menos dos variables.

La tabla de frecuencias absolutas la teníamos calculada del apartado anterior, para obtener la tabla de frecuencias relativa se usa ```prop.table()```  y para la acumulada ```cumsum()```, y como entrada le introducimos la tabla de frecuencias relativa o acumulada. 

```{r}
FrecRel_rango<-round(prop.table(FrecAbs_rango),4)
AbsAcum_rango<-cumsum(FrecAbs_rango)
RelAcum_rango<-cumsum(round(FrecRel_rango,4))
tabla_rango<-cbind(FrecAbs_rango,FrecRel_rango,AbsAcum_rango,RelAcum_rango)
tabla_rango
```

Haciendo lo mismo para la variable *servicio*, obtenemos la siguiente tabla:

```{r include=FALSE}
FrecRel_servicio<-round(prop.table(FrecAbs_servicio),4)
AbsAcum_servicio<-cumsum(FrecAbs_servicio)
RelAcum_servicio<-cumsum(round(FrecRel_servicio,4))

tabla_servicio<-cbind(FrecAbs_servicio,FrecRel_servicio,AbsAcum_servicio,RelAcum_servicio)
```


3.- Aplicar al menos a tres variables las funciones de resumen descriptivo.

Las principales funciones que se utilizan para hacer un análisis descriptivo son ```summary()``` y ```describe()```, para poder acceder a esta última necesitamos cargar la librería *psych*.  Estas son las que se usarán para resolver este apartado. Además de ```mean()```, ```sd()```y ```var()```.

```{r}
summary(salario)
library(psych)
describe(salario)

summary(doctorado)
sd(doctorado)
mean(doctorado)
var(doctorado)
describe(doctorado)

summary(servicio)
describe(servicio)
```


4.- Estudiar los posibles outliers de las variables que lo permitan (al menos
dos variables). Acompañar con la gráficas adecuadas.

Se determina que un dato se considerará anómalo cuando no pertenezca al intervalo que tenga como cota superior 
la media muestral de la variable más dos veces la desviación típica y como cota inferior la media muestral menos dos veces la desviación típica. 

Realizamos el estudio descrito para la variable *salario*. Comenzamos calculando el intervalo. 

```{r}
int <- c(mean(salario)-2*sd(salario),mean(salario)+2*sd(salario))
int 
```

Individuos atípicos:
```{r}
b <- which((salario < int[1]) | (salario > int[2]))
b
```

Salarios anómalos:

```{r}
salario[b]
```



Representamos los datos, 

```{r}
plot(salario)
points(b,salario[b],col="red",pch=3)
```


Ahora estudiamos la variable servicio, basándonos en el método del IQR. En este caso comenzamos representando los datos. 

```{r}
grafico<-boxplot(servicio)
```

Años de servicio anómalos:
```{r}
outliers<-grafico$out
outliers
```

Individuos atípicos:
```{r}
a <- which(servicio %in% outliers)
a
```

\vspace{1cm}
Cabe destacar que el número de outliers que obtenemos por cada uno de los métodos no es el mismo. 


5.-Obtener el histograma de al menos una variable del conjunto de datos.

```{r}
hist(salario,col="coral", breaks = 10,
     main="Histograma de salario", xlab = "Salario", ylab = "Frecuencia absoluta", freq = T)
```


6.- Repetir el apartado anterior en los grupos definidos por otra variable del
conjunto de datos. Usar la librería ggplot2 para los gráficos.

Vamos a dividir la variable *salario* por medio de la variable *rango*, para lo que creamos tres grupos (uno por cada *rango*). 
```{r}
Grupo_prof<-salarios[rango=="Prof",]
Grupo_Asoc<-salarios[rango=="AssocProf",]
Grupo_Asist<-salarios[rango=="AsstProf",]


Salario_prof<-Grupo_prof$salario
Salario_asoc<-Grupo_Asoc$salario
Salario_asist<-Grupo_Asist$salario
```

```{r include=FALSE}
library(ggplot2)
```

A continuación, lo representamos utilizando la librería que se nos pide.
```{r fig.height=4, fig.width=6}
ggplot(Grupo_prof, aes(Salario_prof))+ 
  geom_histogram(breaks=seq(20000, 250000, by = 10000),color="black",fill="darkolivegreen1")+
  labs(x="Salario",  y="Frecuencia", title="Histograma del Salario de los profesores")

ggplot(Grupo_Asist, aes(Salario_asist))+ 
  geom_histogram(breaks=seq(20000, 250000, by = 10000), color="black",fill="darksalmon")+
  labs( title="Histograma del Salario de los asistentes de los profesores", x="Salario", 
        y="Frecuencia")


```

```{r fig.height=3.5, fig.width=6}
ggplot(Grupo_Asoc, aes(Salario_asoc))+ 
  geom_histogram(breaks=seq(20000, 250000, by = 10000), color="black",
                 fill="aquamarine")+
  labs(title = "Histograma del Salario de los profesores asociados", x="Salario",
                 y="Frecuencia")
```


7.- Seleccionar dos variables que permitan realizar un gráfico de dispersión
y un análisis de regresión.


```{r echo=FALSE, fig.height=4, fig.width=6}
pairs(salarios[,c(4,6)], main="Gráfico de dispersión")

```


Realizamos el análisis de regresión mediante la función ```lm()```. 

```{r}
modelo<-lm(salario~servicio, data = salarios)

summary(modelo)
```

De lo que podemos extraer que la variable *salario* no se puede explicar a través de la variable *servicio*. 
