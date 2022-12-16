# Use UnifyQL to query JSON-Unify objects

Works the same in PyUnify (Python) or UnifyX.js (JavaScript)

## UPDATE

The following query will update one cell:
```
unify.query('meta', 'update', [{'row':9, 'col':'value', 'set':"Yahoo Finance Download"}])
```

The following query will update two cells:
```
unify.query('meta', 'update', [{'row':9, 'col':'value', 'set':"Yahoo Finance Download"}, {'row':6, 'col':'value', 'set':'yahoo.com'}])
```

## SELECT

The following query:
```
unify.query('meta', 'select', {'col':'key', 'target':'contract'})
```

Returns:
```
unify.query('meta', 'select', {'col':'key', 'target':'contract'})
```

## INSERT

The following query inserts one row:

```
unify.query('data', 'insert', [{'Animal':'Unicorn', 'Size':'Very Large', 'Gender':None, 'Safe As Pet':True, 'Weight In Pounds':1040.2}])
```
Returns:
```
Row number 5 added
```

The following query inserts two rows:
```
unify.query('data', 'insert', [{'Animal':'Unicorn', 'Size':'Very Large', 'Gender':None, 'Safe As Pet':True, 'Weight In Pounds':1040.2}, {'Animal':'T-Rex', 'Size':'Very, Very Enormous', 'Gender':'Female', 'Safe As Pet':False, 'Weight In Pounds':92283.23}])
```
Returns:
```
Row number 5 added
Row number 6 added
```

## DELETE

The following query will delete the following rows:
```
unify.query('meta', 'delete', [{"rows":[4, 5, 6, 8, 9]}])
```

## PyUnify_Query
```
class PyUnify_Query:
  def __init__(self, json_unify):
    self.json_unify = json_unify
    
  def get_table(self, key):
    if key == 'meta'or key == 'data':
      table = self.json_unify[key]
    elif key == 'headers' or key == 'values' or key == 'features':
      table = self.json_unify['concepts'][key]
    elif key == 'sla' or key == 'requirements':
      table = self.json_unify['governance'][key]
    return table


  # key is the table, cmd is the the action to take like update, insert, select, params are the parameters of the query
  #unify.query('meta', 'update', [{'row':9, 'col':'value', 'set':"Yahoo Finance Download"}, {'row':6, 'col':'value', 'set':'yahoo.com'}])
  def query(self, key, cmd, params):        
    table = self.get_table(key)
    if cmd == 'update':
      return self.query_update(table, params)
    elif cmd == 'select':      
      return self.query_select(table, params)
    elif cmd == 'insert':      
      return self.query_insert(table, params)
    elif cmd == 'delete':      
      return self.query_delete(table, params)

  # unify.query('meta', 'select', {'col':'key', 'target':'source'})
  def query_select(self, table, params):            
    col_name = params['col']    
    column = table[col_name]
    unify_row_index = None 
    retVal = {}   
    for index, item in enumerate(column):
      if item == params['target']:
        retVal['row'] = index
        retVal['column'] = col_name        
        retVal['cell'] = params['target']
    
    return retVal

  # unify.query('meta', 'delete', [{"rows":[4, 5, 6, 8, 9]}])
  def query_delete(self, table, params):    
    
    for param in params:
      if 'rows' in param:
        rows = param['rows']
        for index, row in enumerate(rows):
          
          #for each header delete the list element
          keys = list(table.keys())
                    
          for key in keys:
            
            
            
            del table[key][row-index]
          new_index_length = len(table[keys[0]]) - index        
          
      if 'cols' in param:
        cols = param['cols']
      
    
    
    
  #unify.query('meta', 'update', [{'row':9, 'col':'value', 'set':"Yahoo Finance Download"}, {'row':6, 'col':'value', 'set':'yahoo.com'}])
  def query_update(self, table, params):            
    
    for param in params:
      row = param['row']
      col = param['col']
      set_val = param['set']
      table[col][row] = set_val

  #unify.query('data', 'insert', [{'Animal':'Unicorn', 'Size':'Very Large', 'Gender':None, 'Safe As Pet':True, 'Weight In Pounds':1040.2}])
  def query_insert(self, table, params):                
    length = None
    for param in params:
      keys = list(param.keys())
      for key in keys:
        val = param[key]
        col = table[key]
        length = len(col)
        col.append(val)
      #print("Row number {} added".format(length))
```
