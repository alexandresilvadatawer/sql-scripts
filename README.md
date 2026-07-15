# sql-scripts

## Fluig

1. Busca ML com base no numero do documento do formulario no fluig
   ```sql
   SELECT 
    NR_DOCUMENTO,
  COD_EMPRESA,
  COD_LISTA,
    CONCAT('ML', 
        LPAD(COD_EMPRESA, 3, '0'), 
        LPAD(COD_LISTA, 3, '0')
    ) AS 'Nome da Tabela ML'
FROM 
    DOCUMENTO
WHERE 
    NR_DOCUMENTO = 3430705
    AND COD_LISTA IS NOT NULL
   ```
