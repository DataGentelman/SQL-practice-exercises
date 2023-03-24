# SQL-practice-exercises
SQL practice exercises using the Adventureworksdw2019 databases provided by Microsfot and SQL Server Managment Studio

-- Tener en cuenta que todas las consultas que involucran ventas, se realizan sobre las ventas de Internet (FactInternetSales)

-- 1) Obtener la cantidad de productos vendidos en Internet durante el 2011
SELECT SUM (OrderQuantity) AS TotalProductosVendidosEn2011 
FROM FactInternetSales
WHERE OrderDate BETWEEN '2011-01-01' AND '2011-12-31';
-- Use la función SUM () para calcular la suma de la cantidad de ordenes de productos 'OrderQuantity'.
SELECT SUM (OrderQuantity) AS CantidadProductosVendidos2011
FROM FactInternetSales AS fis
JOIN DimDate AS dd
ON fis.OrderDateKey = dd.DateKey
WHERE YEAR (dd.FullDateAlternateKey) = 2011;
-- Otra opcion podria ser usar el JOIN para relacionar las tablas FactInternetSales y DimDate por la clave OrderDateKey
-- y la clausula WHERE para filtrar los resultados de 011, utilizando la funcion YEAR para estraer el año de la FullDateAlternateKey
-- RESULTADO: 2216 PRODUCTOS VENDIDOS EN 2011 

-- 2) Listar los nombres de productos (EnglishProductoName) vendidos en Mayo del 2011
SELECT dp.EnglishProductName AS ProductosVendidosMayo2011
FROM FactInternetSales AS fis
JOIN DimDate AS dd
ON fis.OrderDateKey = dd.DateKey
JOIN DimProduct AS dp
ON fis.ProductKey = dp.ProductKey
WHERE MONTH (dd.FullDateAlternateKey) = 5
AND YEAR (dd.FullDateAlternateKey) = 2011;
-- En esta query seleccione el EnglsihProcutName de la tabla DimDate, relice un JOIN entre FactInternetSales, DimDate y DimProduct, relacionando las clave. Y utilice la 
-- clausula WHERE junto con las funciones YEAR y MONTH para extraer el mes y el año de dentro de FullDateAlternateKey
-- RESULTADO: 174 REGISTROS DE PRODCUTOS VENDIDOS EN MAYO 2011

-- 3) Listar los nombres de productos (EnglishProductoName) vendidos en Junio del 2011 que en su nombre contengan la palabra 'Black'
SELECT dp.EnglishProductName AS ProdcutosVendidosJuntio2011
FROM FactInternetSales AS fis
JOIN DimDate AS dd
ON fis.OrderDateKey = dd.DateKey
JOIN DimProduct AS dp
ON fis.ProductKey = dp.ProductKey
WHERE MONTH(dd.FullDateAlternateKey) = 6
AND YEAR(dd.FullDateAlternateKey) = 2011
AND dp.EnglishProductName LIKE '%Black%';
-- Realice un JOIN entre las tables FactInternetSales, DimPorduct y DimDate, relacionando sus claves, tambien use la clausula
-- WHERE junto con las funciones MONTH y YEAR para extraer el mes y el año dentro de FullDateAlternateKey. Por ultimo use la clausula
-- LIKE '%%' para que busque cualquier cadena de caracteres que contenga la palabra Black.
-- RESULTADO: 42 REGISTROS DE PRODUCTOS VENDIDOS EN JUNIO 2011

-- 4) Listar los nombres de productos (EnglishProductoName), Subcategorias (EnglishProductSubcategoryName) y Categorias
-- (EnglishProductCategoryName) que fueron vendidos en Mayo del 2011
SELECT dp.EnglishProductName AS Prodcutos, dps.EnglishProductSubcategoryName AS Subcategorias, dpc.EnglishProductCategoryName AS Categorias
FROM FactInternetSales AS fis
JOIN DimDate AS dd
ON fis.OrderDateKey = dd.DateKey
JOIN DimProduct AS dp
ON fis.ProductKey = dp.ProductKey
JOIN DimProductSubcategory AS dps
ON dp.ProductSubcategoryKey = dps.ProductSubcategoryKey
JOIN DimProductCategory AS dpc
ON dps.ProductCategoryKey = dpc.ProductCategoryKey
WHERE MONTH(dd.FullDateAlternateKey) = 5
AND YEAR(dd.FullDateAlternateKey) = 2011;
-- RESULTADO 174 REGISTROS DE PRODUCTOS VENDIDOS EN MAYO 2011 CON SU NOMBRE, SUBCATEGORIA Y CATEGORIA

-- 5) Obtener un listado de los distintos Años de ventas. (OrderDate)
SELECT YEAR(OrderDate) AS Año, SUM(SalesAmount) AS TotalVentasPorAño
FROM FactInternetSales
GROUP BY YEAR(OrderDate)
ORDER BY Año;
-- Utilice la funcion YEAR para extraer el año de la columna OrderDate. Luego use la funcion SUM para sumar el valor de SalesAmount
-- (me parecio relevante para el caso aunque no lo pedia la consigna). Luego agrupe los resultados con la clausula GROUP BY
-- y con ORDER BY alias Año ordene los resultados de menor a mayor.
-- RESULTADO: 5 REGISTROS CON EL IMPORTE TOTAL DE VENTAS POR AÑOS

-- 6) Obtener la cantidad de días de diferencia entre la primer venta y la última venta de internet
SELECT DISTINCT DATEDIFF (Day, MIN (OrderDate), MAX (OrderDate)) AS DiasDeDiferenciaEntrePrimeraYUltimaVenta
FROM FactInternetSales;
-- Use la funcion DATEDIFF para calcular la diferencia de dias entre la primera venta (obtenida usando la funcion MIN) y la
-- ultima (obtenida usando la funcion MAX).
-- RESULTADO: 1126 DIAS DE DIFERENCIA ENTRE LA PRIMERA Y ULTIMA VENTA DE INTERNET

-- 7) Obtener el importe de ventas de internet (SalesAmount) del año 2012. Usar la función de agregación SUM
SELECT SUM (SalesAmount) AS TotalVentas2012
FROM FactInternetSales
WHERE OrderDate BETWEEN '2012-01-01' AND '2012-12-31'; 
-- Otra opcion
SELECT SUM (SalesAmount) AS TotalVentas2012
FROM FactInternetSales
WHERE YEAR (OrderDate) = 2012; 
-- RESULTADO: $5.842.485

-- 8) Listar las ventas que fueron realizadas en United States (SalesTerritoryCountry)
SELECT SalesAmount AS VentasEnUnitedStates
FROM FactInternetSales AS fis
JOIN DimSalesTerritory AS dst
ON fis.SalesTerritoryKey = dst.SalesTerritoryKey
WHERE dst.SalesTerritoryCountry = 'United States';
-- Realice un JOIN entre FactInternetSales y DimSalesTerritory, relacionando sus claves y use la clausula WHERE para
-- para que solo aparezcan los SalesTerritoryCountry que coincidan con United States
-- RESULTADO: 21344 REGISTROS QUE CORRESPONDEN A LAS VENTA REALIZADAS EN UNITED STATES

-- 9) Listar las regiones (SalesTerritoryRegion) donde se realizaron las ventas del 2012
SELECT DISTINCT dst.SalesTerritoryRegion AS Regiones
FROM FactInternetSales AS fis
JOIN DimSalesTerritory AS dst
ON fis.SalesTerritoryKey = dst.SalesTerritoryKey
WHERE YEAR(fis.OrderDate) = 2012;
-- Hice un JOIN entre FactInternetSales y DimSalesTerritory relacionando sus claves, luego use la clausula WHERE junto con la
-- funcion YEAR para filtrar los resultados de 2012. Use la funcion DISTINCT para que solo me muestre 1 vez el nombre de las regiones.
-- RESULTADO: 10 REGISTROS QUE CORRESPONDEN A LA REGIONES DONDE SE REALIZARON VENTAS EN 2012

-- 10) Listar las ventas de los productos que contienen 'Red' en su nombre,
-- con que fueron realizadas en United States (SalesTerritoryCountry) en el año 2012.
SELECT (SalesOrderNumber) AS NumeroOrdenVenta, (EnglishProductName) AS ProductRed, (OrderDate) AS Fecha
FROM FactInternetSales AS fis
JOIN DimProduct AS dp
ON fis.ProductKey = dp.ProductKey
JOIN DimDate AS dd
ON fis.OrderDateKey = dd.DateKey
JOIN DimSalesTerritory AS dst
ON fis.SalesTerritoryKey = dst.SalesTerritoryKey
WHERE dp.EnglishProductName LIKE '%Red%' 
AND dst.SalesTerritoryCountry = 'United States'
AND YEAR (fis.OrderDate) = '2012';
-- Realice un JOIN entre las tablas FactInternetSales, DimPorduct, DimDate y DimSalesTerritory, entre sus claves. Use la clausula
-- WHERE con la funcion LIKE '%%' para filtrar los productos que contengan Red, y la funcion YEAR para filtrar las ordenes realizadas en 2012.
-- RESULTADO: 214 REGISTROS QUE CORRESPONDEN A PRODUCTOS QUE CONTENGAN RED EN SU NOMBRE, VENDIDOS EN UNITED STATES EN 2012.

-- 11) Listar las ventas que fueron realizadas en Pesos Argentinos ('Argentine Peso')
SELECT (SalesOrderNumber)
FROM FactInternetSales AS fis
JOIN DimCurrency AS dc
ON fis.CurrencyKey = dc.CurrencyKey
WHERE dc.CurrencyName = 'Argentine Peso';
-- Hice un JOIN entre FactInternetSales y DimCurrecny relacionando sus claves y utilice la clausula WHERE para filtrar
-- los valores dentro de la columna CurrencyName que coincidan con Argentine Peso.
-- RESULTADO: 0 REGISTROS, LO QUE ME DIRIA QUE NO SE REALIZARON VENTAS EN PESOS ARGENTINOS

-- 12) Listar las ventas que fueron realizadas en Dólares Norteamericanos ('US Dollar')
SELECT (SalesOrderNumber) AS CodVenta, CurrencyName AS NombreMoneda
FROM FactInternetSales AS fis
JOIN DimCurrency AS dc
ON fis.CurrencyKey = dc.CurrencyKey
WHERE dc.CurrencyName = 'US Dollar';
-- Hice un JOIN entre FactInternetSales y DimCurrecny relacionando sus claves y utilice la clausula WHERE para filtrar
-- los valores dentro de la columna CurrencyName que coincidan con US Dollar.
-- RESULTADO 33400 REGISTROS QUE CORRESPONDEN A LAS VENTAS REALIZADAS EN DOLARES NORTEAMERICANOS
