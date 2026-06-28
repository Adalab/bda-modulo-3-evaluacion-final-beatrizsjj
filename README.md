# bda-modulo-3-evaluacion-final-beatrizsjj
bda-modulo-3-evaluacion-final-beatrizsjj created by GitHub Classroom

- Unión columnas df_activity mes y año para tener fecha completa
- elimino columna mes (me parece poco importante), dejo la de año por si necesitara hacer una división por años y me resultara más fácil
- En activity hay 228705 filas con todo 0, que indicarían que ese mes no hubo actividad registrada (por eso no los elimino, ya que en realidad no se trataría de valores nulos y puede darnos información importante)
- loyalty pasamos la columna cancellation year y month a números enteros porque estaban en float y luego unimos en fecha
- Después elimino columnas enrollment month y cancellation month
- Compruebo que en loyalty no hay duplicados