# sql-scripts

## Fluig

### Busca ML do formulario no fluig
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
### Inner join com a tabela DOCUMENTO
```sql
INNER JOIN DOCUMENTO DOC
    ON DOC.NUM_DOCTO_PROPRIED = ML.CARDID
   AND DOC.NR_DOCUMENTO = ML.DOCUMENTID
   AND DOC.NR_VERSAO = ML.VERSION
   AND DOC.VERSAO_ATIVA = 1
   AND DOC.TP_DOCUMENTO = '5'
   AND DOC.COD_LISTA = 32
```

### Dataset Libera novo campo do formulario
Atualiza os formularios para a versao específica
```javascript
function createDataset(fields, constraints, sortFields) {
    var dataSource = "java:/jdbc/FluigDS"; // DataSource padrão do Fluig
    var conn = null;
    var stmt = null;
    
    try {
        var connectionService = javax.naming.InitialContext.doLookup(dataSource);
        conn = connectionService.getConnection();
        
        var sql = `\n\n
            UPDATE DOCUMENTO 
                SET NUM_VERS_PROPRIED = versaoAtual
                
            WHERE 
                VERSAO_ATIVA = 1        
                AND NUM_DOCTO_PROPRIED = parentId
                
            `;
        stmt = conn.prepareStatement(sql);
        stmt.executeUpdate();
    } catch (e) {
        log.error("Erro ao atualizar campo: " + e);
    } finally {
        if (stmt != null) stmt.close();
        if (conn != null) conn.close();
    }
    return null;
}

```
