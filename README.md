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
        COD_LISTA
    ) AS 'Nome da Tabela ML'
FROM 
    DOCUMENTO
WHERE 
    NR_DOCUMENTO = 3430705
    AND COD_LISTA IS NOT NULL
```
1. inner join com a tabela DOCUMENTO para buscar os registros ativos da ML
```sql
INNER JOIN DOCUMENTO DOC
    ON DOC.NUM_DOCTO_PROPRIED = ML.CARDID
   AND DOC.NR_DOCUMENTO = ML.DOCUMENTID
   AND DOC.NR_VERSAO = ML.VERSION
   AND DOC.VERSAO_ATIVA = 1
   AND DOC.TP_DOCUMENTO = '5'
   AND DOC.COD_LISTA = 32
```
