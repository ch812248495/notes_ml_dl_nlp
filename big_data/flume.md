# flume
## flueme写入 hbase
```java

    public List<PutRequest> getActions() {
        List<PutRequest> actions = new ArrayList<PutRequest>();
        if (payloadColumn != null) {
            byte[] rowKey;
            try {
                String[] columns = String.valueOf(payloadColumn).split(",");
                String[] values = String.valueOf(this.payload).split(",");

                for(int i=0; i < columns.length; i++){
                    byte[] colColumn = columns[i].getBytes();
                    byte[] colValue = values[i].getBytes(Charsets.UTF_8);
                    if(colColumn.length != colValue.length) break;
                    String datetime = values[0].toString();
                    String userid = values[1].toString();
                    rowKey = SimpleRowKeyGenerator.getKfkRowKey(userid,datetime);
                    PutRequest putRequest =  new PutRequest(table, rowKey, cf,
                            colColumn, colValue);
                    actions.add(putRequest);
                }
            } catch (Exception e) {
                throw new FlumeException("Could not get row key!", e);
            }
        }
        return actions;
    }

public static byte[] getKfkRowKey(String userid,String datetime) throws UnsupportedEncodingException {
    return (userid + datetime + String.valueOf(System.currentTimeMillis())).getBytes("UTF8");
  }
```