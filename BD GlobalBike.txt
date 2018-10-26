Create Database GolbalBike

Use GolbalBike

Create Table Lenguajes (
LLave_Lenguaje Varchar(2) Primary Key,
Lenguaje       Varchar(2),
Pais           Varchar(2)
)

Create Table Organizaciones_Venta (
Id_Organizacion  Varchar(4)   Primary Key,
LLave_Lenguaje   Varchar(2)   Foreign Key References Lenguajes(LLave_Lenguaje),
Descripcion      Varchar(Max)
)

Create Table Categoria_Producto (
Id_Cat_Prod      Varchar(3)   Primary Key,
LLave_Lenguaje   Varchar(2)   Foreign Key References Lenguajes(LLave_Lenguaje),
Descripcion      Varchar(Max)
)

Create Table Materiales (
Descripcion   Varchar(Max),
Id_Material   Varchar(8)  Primary Key,
Id_Cat_Prod   Varchar(3)  Foreign Key References Categoria_Producto(Id_Cat_Prod),
Division      Varchar(2)
)

Create Table Clientes(
Id_Cliente        Int          Primary Key,
LLave_Lenguaje    Varchar(2)   Foreign Key References Lenguajes(LLave_Lenguaje),
Id_Organizacion   Varchar(4)   Foreign Key References Organizaciones_Venta(Id_Organizacion),
Locacion          Varchar(Max),
Pais              Varchar(2),
Descripcion       Varchar(Max)
)

Create Table Meses(
Mes      Int           Primary Key,
Nombre   Varchar(10)
)

Insert Into Meses Values ('1', 'Enero')
Insert Into Meses Values ('2', 'Febrero')
Insert Into Meses Values ('3', 'Marzo')
Insert Into Meses Values ('4', 'Abril')
Insert Into Meses Values ('5', 'Mayo')
Insert Into Meses Values ('6', 'Junio')
Insert Into Meses Values ('7', 'Julio')
Insert Into Meses Values ('8', 'Agosto')
Insert Into Meses Values ('9', 'Septiembre')
Insert Into Meses Values ('10', 'Octubre')
Insert Into Meses Values ('11', 'Noviembre')
Insert Into Meses Values ('12', 'Diciembre')


Create Table Transacciones_Venta(
No_Orden             Int,
No_Item              Int,
Año                  Int,
Mes                  Int                 Foreign Key References Meses(Mes),
Fecha                DateTime,
Id_Cliente           Int                 Foreign Key References Clientes(Id_Cliente),
Producto             VarChar(8)          Foreign Key References Materiales(Id_Material),
Cantidad             Int,
Unidad_Medicion      VarChar(2),
Ganancia_USD         Money,
Descuento_USD        Money,
Costo_USD            Money,
Constraint PK_Venta Primary Key (No_Orden, No_Item)
)

--Creacion de la nueva Tabla Descripcion_De_Bicis
Create Table Descripcion_de_Bicis(
Id_Producto             Varchar(8)     Foreign Key References Materiales(Id_Material) Not Null,
Morfologia_de_Cuadro    Varchar (Max),
Tipo_Freno              Varchar(Max),
Tipo_Manillar           Varchar(Max),
Suspension              Char(2),
Suspension_Silla        Char(2),
Potencia                Char(2),
No_Velocidades          SmallInt,
Tallas                  Char(2),
Colores                 Char(2),
Precio_USD              Money,
Peso_KG                 Float,
Cuadro                  Varchar(Max),
Horquilla               Varchar(Max),
Potencia_Material       Varchar(Max),
Llantas                 Varchar(Max),
Tija                    Varchar(Max),
Manillar                Varchar(Max),
Guardafangos            Char(2),
protector_De_Cadena     Char(2),
Timbre                  Char(2),
Luz_Delantera           Char(2),
Luz_Trasera             Char(2),
Canasta                 Char(2),
Parilla                 Char(2),
Caballete               Char(2)
Primary key(Id_Producto)
)

--Querys
--Tabla de Categorias
select * from Categoria_Producto

--Tabla de materiales
select * from Materiales

--Tabla de clientes
select * from Clientes

--Tabla Organizaciones
select * from Organizaciones_Venta

--Clave, Localizacion y Nombre de clientes de Estados Unidos
select Id_Cliente, Locacion, Pais, Descripcion
from Clientes
where Pais='US'

--Materiales en la categoria Accesories
select b.Descripcion,b.Id_Cat_Prod
from Categoria_Producto as a, Materiales as b
where a.Id_Cat_Prod=b.Id_Cat_Prod and b.Id_Cat_Prod='ACC'

--Productos mas Vendidos en Marzo
select top 3 m.Descripcion, count (v.Producto)*v.Cantidad as total_ventas
from Materiales as M, Transacciones_Venta as v
where m.Id_Material=v.Producto and v.Mes='3'
group by m.Descripcion, v.Cantidad
order by total_ventas desc

--Cliente que nos a dejado mas ganancia
select top 1 a.Descripcion, sum(b.Ganancia_USD) as ganancia
from Clientes as a, Transacciones_Venta as b
where a.Id_Cliente=b.Id_Cliente
group by a.Descripcion, b.Id_Cliente
order by Ganancia 

--Clientes en la organizacion UE00
select a.Descripcion, a.Id_Organizacion, b.Descripcion
from Clientes as a, Organizaciones_Venta as b
where a.Id_Organizacion=b.Id_Organizacion and a.Id_Organizacion='UE00'

--Los 6 meses con menos ventas
select top 6 b.Nombre, count(a.Mes) as Total
from Transacciones_Venta as A, Meses as B
where a.Mes=b.Mes
group by b.Nombre
order by Total asc

--Cantidad de productos por categoria ordenados de menor a mayor
select top 6 b.Descripcion, count(a.Id_Cat_Prod) as Total
from Materiales as A, Categoria_Producto as B
where a.Id_Cat_Prod=b.Id_Cat_Prod
group by b.Descripcion
order by Total asc

--Que Materiales compro el cliente con id=17000 en la fecha 1 de enero de 2007
select b.Descripcion, a.Descripcion, c.Id_Cliente
from Clientes as A, Materiales as B, Transacciones_Venta as C
where b.Id_Material=c.Producto and a.Id_Cliente=c.Id_Cliente and c.Id_Cliente='17000' and c.Fecha='2007-01-01'

--Procediminetos
--Procedimientos para CrystalReports


--Nuevos Querys
--Lista del Cliente 21,000 Q1
Select * From Transacciones_Venta 
Where Id_Cliente = 21000

--Calcular Costo Total Q2
Select sum (Costo_USD) As total
From Transacciones_Venta 
Where Id_Cliente = 21000

--Ordernar Clientes Ascendente Q3
Select C.Id_Cliente
From Transacciones_Venta As V, Clientes As C 
Where c.Id_Cliente=v.Id_Cliente
Order by c.Id_Cliente asc


--Productos Comprados X El Cliente 21,000 Q4
Select TV.Producto, CP.Descripcion
From Transacciones_Venta As TV, Categoria_Producto As CP
Where TV.Id_Cliente = 21000

--Productos Comprados X El Cliente 21,000 + Costo_USD Total Q5
SELECT TV.Producto AS ID, M.Descripcion AS NOMBRE, TEMP.COSTOTOTAL AS TOTALGRAL
FROM (SELECT  SUM (TV.Costo_USD) AS COSTOTOTAL 
FROM  Transacciones_Venta AS TV
WHERE TV.Id_Cliente = 21000) AS TEMP,Transacciones_Venta AS TV, Materiales AS M
WHERE TV.Id_Cliente = 21000 AND TV.Producto = M.Id_Material 
GROUP BY Producto, Descripcion, TEMP.COSTOTOTAL

--Realizar Una Consulta Que Muestre El Nombre De Los Clientes De US Y Que Han Comprado Al Menos Un Artículo Q6
SELECT DISTINCT C.Id_Cliente, C.Locacion
FROM Transacciones_Venta AS V, Clientes AS C
WHERE V.Id_Cliente = C.Id_Cliente AND C.Pais = 'US'

--Producto Más Aparece En La Tabla Transacciones_Venta Q7
SELECT TOP 1 M.Id_Material, M.Descripcion, COUNT(V.Producto) AS Total_Vendido
FROM Materiales AS M, Transacciones_Venta AS V
WHERE M.Id_Material = V.Producto
GROUP BY M.Id_Material, M.Descripcion
ORDER BY Total_Vendido DESC

--Desplegar La Cantidad De Productos Que Tuvo Un Cliente En El Siguiente Rango De Años 2007-2010 Q8
SELECT M.Id_Material as Identificador, M.Descripcion as NOMBRE ,count(T.Producto) as CANTIDAD
FROM Transacciones_Venta AS T,Materiales AS M
WHERE T.Año BETWEEN 2007 AND 2010 AND M.Id_Material= T.Producto AND T.Id_Cliente = 21000
GROUP BY M.Id_Material, M.Descripcion,T.Producto

--Mostrar El Nombre Del Cliente, Pais, Organización, Producto (Nombre), Categoría Y El Número De Ítem De Las Transacciones De Un Cliente Q9
SELECT CL.Descripcion AS CLIENTE, Cl.Pais AS PAIS, ORG.Descripcion	 AS ORGANIZACION,M.Descripcion AS PRODUCTO, CP.Descripcion AS CATEGORIA, TV.No_Item 
FROM Transacciones_Venta AS TV, Materiales AS M, Clientes AS Cl, Categoria_Producto AS CP, Organizaciones_Venta AS ORG
WHERE TV.Id_Cliente = 21000 AND TV.Id_Cliente = Cl.Id_Cliente AND TV.Producto = M.Id_Material AND CL.Id_Organizacion = ORG.Id_Organizacion AND M.Id_Cat_Prod = CP.Id_Cat_Prod

-- Cantidad De Productos Total Comprados Por Un Cliente Por Orden Q10
SELECT No_Orden, SUM(Cantidad) AS CANTIDAD_DE_PRODUCTOS
FROM Transacciones_Venta
WHERE  Id_Cliente = 21000
GROUP BY No_Orden
ORDER BY No_Orden ASC