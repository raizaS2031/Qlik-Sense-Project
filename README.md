
# Finanzas Personales-Dashboard

Análisis del comportamiento de las finanzas personales mensualmente.

# Objetivo
Diseñar un reporte con indicadores en Qlik Sense que ayuden a dar seguimiento del comportamiento de los ingresos y egresos del mes actual contra el anterior para contribuir a metas financieras a corto y mediano plazo .


# Términos y Definiciones
- Ingresos: Cantidad monetaria percibida mensualmente.

- Egresos: Cantidad monetaria gastada mensualmente.

- Gastos fijos: Referente a los gastos que se consideran cada mes y por lo general no varían.

- Regla 50/30/20: Métrica que establece una forma de distribución de los ingresos, siendo el 50% para gastos fijos, el 30% para gustos y el 20% para el ahorro. 

- Variación: Es la diferencia entre lo que indica la regla 50/30/20 que se tiene que destinar a gastos fijos (50% de ingresos) o a los gustos (30% de ingresos) y lo que realmente se destinó, puede ser más o menos.

# Información proporcionada
- Ingresos mensuales
- Egresos mensuales

# Fuente
Los datos de este proyecto se almacenaron en una hoja de cálculo de excel y tiene la siguiente estructura.

![fuente](https://github.com/raizaS2031/Qlik-Sense-Project/assets/167453260/6d41a1db-95f8-4d19-93a3-d4b4531c217a)

# Solución

## Carga de datos
Se creó una una app en Qlik sense donde se conectó a la ruta que contiene el archivo excel.

## Código
1. Dentro de la app se creó la sección Monthly Budget que contiene la información del archivo excel.

![codigo 1](https://github.com/raizaS2031/Qlik-Sense-Project/assets/167453260/926c7a55-4e08-4d18-9cfc-291c89d8a2f9)


2. Usando buenas prácticas, se aplicó una transposición a la tabla para facilitar el modelado de datos.

	![codigo 2](https://github.com/raizaS2031/Qlik-Sense-Project/assets/167453260/0fc9a5dc-dfc9-4e43-9959-727b4bb9e507)

3. Se creó una nueva tabla para realizar un mapeo entre los campos de la tabla Presupuesto_Transpuesta con las tablas CALENDARIO y TABLA_INGRESOS (colocada en la sección Ingresos), de donde se obtuvieron los campos AÑO, %FECH, FECHA Y MESNUM. 

	![codigo 3](https://github.com/raizaS2031/Qlik-Sense-Project/assets/167453260/b959ae16-572c-4908-b79a-b60b962b8c1e)

	![codigo 4](https://github.com/raizaS2031/Qlik-Sense-Project/assets/167453260/11170cd3-46ec-42a0-bb3d-a1cc3d860b5a)

4. Se hizo un mapeo entre la tabla Presupuesto_Transpuesta y la tabla TABLA_GUSTOS (esta tabla contiene los registros de los egresos que son un ‘extra’ de los gastos fijos) mediante la llave %GUSTOS.

![codigo 5](https://github.com/raizaS2031/Qlik-Sense-Project/assets/167453260/cf4fe668-f92d-41db-835d-82d6b6d47216)

5. En la última parte del código se creó una tabla temporal que calcula el monto máximo gastado para futuros análisis comparativos entre los egresos.

![codigo 6](https://github.com/raizaS2031/Qlik-Sense-Project/assets/167453260/23c1a659-ceff-4881-bffa-45a93e04318d)

# Resultados
## Modelo de datos

![resultados](https://github.com/raizaS2031/Qlik-Sense-Project/assets/167453260/1deb40b4-9885-4b73-9b6d-f11e28053727)

### Se realiza un análisis de datos para responder gráficamente a las siguientes preguntas:
1. ¿Cuál es el mayor gasto fijo?

2. ¿Cómo se distribuyen los ingresos de acuerdo a la regla 50/30/20?

3. ¿En qué mes del 2024 ha tenido el mayor ahorro hasta ahora?


## Set Analysis 
tdc abono: 

        Sum({$<BILLS={'tdc'}>}MONTO)

ingresos:

        Sum(INCOME)

egresos:

        Sum(MONTO)

Balance antes de ahorro

        sum(INCOME)-(SUM(MONTO)+SUM(IMPORTE))

Balance después del ahorro

        sum(INCOME)-(SUM(MONTO)+SUM(IMPORTE))-(SUM(INCOME)*.2)

50% gastos fijos

        SUM(INCOME)*.5

30% gustos

        SUM(INCOME)*.3

20% ahorro

        SUM(INCOME)*.2

variación gastos fijos

        SUM(INCOME)*.5-SUM(MONTO)

variación gustos

        SUM(INCOME)*.3-SUM(IMPORTE)

¿Cuánto llevo ahorrado?

        sum({<FECHA={"<=$(=Max(FECHA))"}>}(INCOME*.2))

¿Cuánto puedo gastar este mes?

        SUM({$<FECHA={"$(=max(FECHA))"}>}INCOME*.5)

¿Cuánto podía gastar el mes anterior?: 

        SUM({$<FECHA={"$(=max(FECHA)-1)"}>}INCOME*.5)

variación entre presupuesto y gasto del mes actual

        SUM({<FECHA={"$(=MAX(FECHA))"}>}INCOME*.5)-SUM({<FECHA={"$(=MAX(FECHA))"}>}MONTO)

variación entre presupuesto y gasto del mes anterior: 

        SUM({$<FECHA={"$(=max(FECHA)-1)"}>}INCOME*.5)- SUM({$<FECHA={"$(=max(FECHA)-1)"}>}MONTO)

## Visualización de datos
- Desgloce de gastos fijos mensualmente

![main_dashboard](https://github.com/raizaS2031/Qlik-Sense-Project/assets/167453260/84093e5e-ad0a-45f2-b315-9f29dd1db790)

- KPI's de ingresos y egresos de acuerdo a la regla del 50/30/20

![budget_dashboard](https://github.com/raizaS2031/Qlik-Sense-Project/assets/167453260/da217fd8-36e4-482f-b9ac-379c70b10ae6)

- Variaciones de gastos entre el mes actual y el anterior

![analisis-dashboard](https://github.com/raizaS2031/Qlik-Sense-Project/assets/167453260/00f235d7-4cea-4d4f-b672-77b7b057fb56)

# Insights
I. El gasto más significativo de enero a junio del 2024 es el de la despensa.

		Con un 44.4% que equivale a $11,490

II. De acuardo a la regla del 50/30/20 de enero a junio los ingresos se distribuyeron como se muestra a continuación.

		a) 50% Gastos fijos equivale a $28,373.00
		b) 30% de Gustos equivale a $17,023.80
		c) 20% de Ahorro equivale a $11,349.00

III. Observando la gráfica de ahorro de cada mes podemos apreciar que en los primeros dos meses del año se mantiene un ahorro constante, pero a partir de febrero a junio se observa una tendencia a la baja.

		Entonces, los meses con mayor ahorro hasta ahora han sido enero y febrero con $2,437.00.

# Conclusión

En conclusión, más allá de los cálculos ofrecidos por este proyecto, su idea central radica en concebirlo como una herramienta fundamental para iniciar la construcción de finanzas personales sólidas que nos orienten hacia metas alcanzables. Esto puede abarcar desde la capacidad de adquirir bienes materiales hasta la proyección de un futuro con mayor seguridad financiera.
