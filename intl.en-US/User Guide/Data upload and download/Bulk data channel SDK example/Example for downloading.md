# Example for downloading {#concept_jzt_3ng_vdb .concept}

```
import java.io.IOException;
 import java.util.Date;
 import com.aliyun.odps.Column;
 import com.aliyun.odps.Odps;
 import com.aliyun.odps.PartitionSpec;
 import com.aliyun.odps.TableSchema;
 import com.aliyun.odps.account.Account;
 import com.aliyun.odps.account.AliyunAccount;
 import com.aliyun.odps.data.Record;
 import com.aliyun.odps.data.RecordReader;
 import com.aliyun.odps.tunnel.TableTunnel;
 import com.aliyun.odps.tunnel.TableTunnel.DownloadSession;
 import com.aliyun.odps.tunnel.TunnelException;
 public class DownloadSample {
         private static String accessId = "<your access id>";
         private static String accessKey = "<your access Key>";
         private static String odpsUrl = "http://service.odps.aliyun.com/api";
         private static String tunnelUrl = "http://dt.cn-shanghai.maxcompute.aliyun-inc.com";
                         //The tunnelURL must be set if you need to connect internal network, otherwise, the system uses public network as default.  The example shows the Tunnel Endpoint of classical network in HuaDong 2, for other regions, see Access domain and data centers.
         private static String project = "<your project>";
         private static String table = "<your table name>";
         private static String partition = "<your partition spec>";
         public static void main(String args[]) {
                 Account account = new AliyunAccount(accessId, accessKey);
                 Odps odps = new Odps(account);
                 odps.setEndpoint(odpsUrl);
                 odps.setDefaultProject(project);
                 TableTunnel tunnel = new TableTunnel(odps);
                 tunnel.setEndpoint(tunnelUrl);//set tunnelUrl
                 PartitionSpec partitionSpec = new PartitionSpec(partition);
                 try {
                         DownloadSession downloadSession = tunnel.createDownloadSession(project, table,
                                         partitionSpec);
                         System.out.println("Session Status is : "
                                         + downloadSession.getStatus().toString());
                         long count = downloadSession.getRecordCount();
                         System.out.println("RecordCount is: " + count);
                         RecordReader recordReader = downloadSession.openRecordReader(0,
                                         count);
                         Record record;
                         while ((record = recordReader.read()) ! = null) {
                                 consumeRecord(record, downloadSession.getSchema());
                         
                         recordReader.close();
                 } catch (TunnelException e) {
                         e.printStackTrace();
                 } catch (IOException e1) {
                         e1.printStackTrace();
                 
         
         private static void consumeRecord(Record record, TableSchema schema) {
                 for (int i = 0; i < schema.getColumns().size(); i++) {
                         Column column = schema.getColumn(i);
                         String colValue = null;
                         switch (column.getType()) {
                         case BIGINT: {
                                 Long v = record.getBigint(i);
                                 colValue = v == null ? null : v.toString();
                                 break;
                         
                         case BOOLEAN: {
                                 Boolean v = record.getBoolean(i);
                                 colValue = v == null ? null : v.toString();
                                 break;
                         
                         case DATETIME: {
                                 Date v = record.getDatetime(i);
                                 colValue = v == null ? null : v.toString();
                                 break;
                         
                         case DOUBLE: {
                                 Double v = record.getDouble(i);
                                 colValue = v == null ? null : v.toString();
                                 break;
                         
                         case STRING: {
                                 String v = record.getString(i);
                                 colValue = v == null ? null : v.toString();
                                 break;
                         
                         default:
                                 throw new RuntimeException("Unknown column type: "
                                                 + column.getType());
                         
                         System.out.print(colValue == null ? "null" : colValue);
                         if (i ! = schema.getColumns().size())
                                 System.out.print("\t");
                 
                 System.out.println();
         
 
```

In this example, data is directly printed using System.out.println to facilitate testing. In actual use, you can directly output the data to a text file.

