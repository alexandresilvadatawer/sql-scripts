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

## Dataset de SQL Update, Insert, Delete
```javascript
function createDataset ( fields, constraints, sortFields) {
	try {
		var minhaQuery = fields[0];
		var dataSource = fields[1];
		
		var newDataset = DatasetBuilder.newDataset();
		var ic = new javax.naming.InitialContext();
		var ds = ic.lookup(dataSource);
		var created = false;
		
		var conn = ds.getConnection();
		var stmt = conn.createStatement();
		
		//if(/INSERT INTO/i.test(minhaQuery) || /UPDATE/i.test(minhaQuery) || /DELETE/i.test(minhaQuery)){
		
		var rs = stmt.execute(minhaQuery);
		
		newDataset.addColumn("RESULTADO");
		newDataset.addColumn("MENSAGEM");
		newDataset.addRow(new Array("OK", "Registro inserido alterado excluido com sucesso."));
	
	} catch(e) {
		var newDataset = DatasetBuilder.newDataset();
		newDataset.addColumn("ERRO");
		newDataset.addRow(new Array(e.toString()));
		
	} finally {
		if ( stmt != null && stmt != undefined ) stmt.close();
		if ( conn != null && conn != undefined ) conn.close();
	}
	
	return newDataset;
}

// EXEMPLO DE USO
{
    var sql= `\n\n
   
UPDATE TAR_PROCES AS TP

INNER JOIN PROCES_WORKFLOW AS PW
    ON PW.NUM_PROCES = TP.NUM_PROCES
	
SET
    TP.LOG_ATIV = 0
	
WHERE 
	1 = 1
	AND TP.LOG_ATIV = 1
	AND PW.NUM_PROCES IN (17341,41230,542335,910944)
    `;
    var ds = DatasetFactory.getDataset("ds_dw_Sql_Update", new Array(sql, "java:/jdbc/AppDS"), null, null);
}
```
### Dataset de SQL Select
```javascript
function createDataset ( fields, constraints, sortFields) {
	try {
		var minhaQuery = fields[0];
		var dataSource = fields[1];       
	
		var newDataset = DatasetBuilder.newDataset();
		var ic = new javax.naming.InitialContext();
		var ds = ic.lookup(dataSource);
		var created = false;
	
	   var conn = ds.getConnection();
	   var stmt = conn.createStatement();
	   var rs = stmt.executeQuery(minhaQuery);
	   var columnCount = rs.getMetaData().getColumnCount();
	   
	   while ( rs.next() ) {
		   if ( !created ) {
			   for ( var i = 1; i <= columnCount; i++) {
				  newDataset.addColumn(rs.getMetaData().getColumnName(i));
			   }
			   
			   created = true;
		   }
		   
		   var Arr = new Array();
		   for ( var i = 1; i <= columnCount; i++) {
			   var obj = rs.getObject(rs.getMetaData().getColumnName(i));
			   
			   if ( null != obj ) {
			  	  Arr[i-1] = rs.getObject(rs.getMetaData().getColumnName(i)).toString();
			  	  
			   } else {
				  Arr[i-1] = "null";
			   }
		   }
		   
		   newDataset.addRow(Arr);
	   }
	   
	} catch (e) {
		var newDataset = DatasetBuilder.newDataset();
		newDataset.addColumn("ERRO");
		newDataset.addRow(new Array(e.toString()));
		
	} finally {
	   if ( stmt != null ) stmt.close();
	   if ( conn != null ) conn.close();                     
	}
	
	return newDataset;
}

//EXEMPLO DE USO
{
    var sql= `\n\n
    SELECT * FROM BKP_HISTOR_PROCES LIMIT 1
    `;
    DatasetFactory.getDataset("ds_dw_Sql", new Array(sql, "java:/jdbc/AppDS"), null, null);
}
```
