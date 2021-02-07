## **```VB.Net Example```**

### Table of Contents
* ***[Asynchronous Example](#example-request-asynchronous-operation)***
     * [Function Helper Async](#example-request-asynchronous-operation)
       * [GetResponseAsync(address As String) As Task(Of String)](#example-request-asynchronous-operation)  
       * [GetMarketPairsAsync As Task(Of List(Of String))](#function-GetMarketPairsAsync)
       * [GetLastBTCAsync As Task(Of Double)](#function-GetLastBTCAsync)
     * [Usage Async Operation](#example-usage-async-operation)
* ***[Synchronous Example](#example-request-synchronous-operation)***
     * [Function Helper Sync](#example-request-synchronous-operation)
       * [GetResponseSync(address As String) As String](#example-request-synchronous-operation)  
       * [GetMarketPairsSync As List(Of String)](#function-GetMarketPairsSync)
       * [GetLastBTCSync As Double](#function-GetLastBTCSync)
     * [Usage Sync Operation](#example-usage-sync-operation)
* ***[Full Code with Class Helper](#full-code-with-class-helper)***

---
### Prerequisites 
 [Json.Net Library](https//github.com/JamesNK/Newtonsoft.Json) 
* [NuGet Package](https://www.nuget.org/packages/Newtonsoft.Json)
* ***.Net Framework 4.5 Or higher*** 

---
### Example Request Asynchronous Operation

##### `Function GetResponseAsync(address As String)`
<details> <summary> Click To expand</summary>
    
```vb
    Private Async Function GetResponseAsync(address As String) As Task(Of String)
    'declare variable for output
    Dim result As String = Nothing

    ' Create a request for the URL. 
    Dim request As WebRequest = WebRequest.Create(address)
    ' Set the Method property of the request to GET. 
    request.Method = "GET"
    ' Get the response.  
    Using response As WebResponse = Await request.GetResponseAsync()
        ' Display the status.  
        'Example: OK
        Console.WriteLine(CType(response, HttpWebResponse).StatusDescription)

        ' Get the stream containing content returned by the server. 
        Using dataStream As Stream = response.GetResponseStream()
            ' Open the stream using a StreamReader for easy access.  
            Using reader As New StreamReader(dataStream)
                ' Read the content.  
                result = Await reader.ReadToEndAsync()
            End Using
        End Using
    End Using

    'return the output
    Return result

End Function
```
</details>

##### `Function GetMarketPairsAsync`
<details>
<summary> Click To expand</summary>
    
```vb
    Public Async Function GetMarketPairsAsync() As Task(Of List(Of String))

    'get JSON String
    Dim JsonString As String = Await GetResponseAsync("https://indodax.com/api/pairs")

    'parse raw JSON string as JArray
    Dim JsonArray As JArray = JArray.Parse(JsonString)

    'linq create list of string
    Dim result As List(Of String) = (From x As JToken In JsonArray
                                     Select x("description").ToObject(Of String)).ToList

    Return result

End Function
```
</details>

##### `Function GetLastBTCAsync`
<details>
<summary> Click To expand</summary>
    
```vb
Public Async Function GetLastBTCAsync() As Task(Of Double)

    'get JSON String
    Dim JsonString As String = Await GetResponseAsync("https://indodax.com/api/ticker/btcidr")

    'parse raw JSON string as JObject
    Dim JsonObj As JObject = JObject.Parse(JsonString)

    'linq create list of string
    Dim result As Double = JsonObj("ticker")("last")

    Return result
End Function

```
</details>

##### `Example Usage Async Operation`
<details>
<summary> Click To expand</summary>
    
```vb
Imports System.IO
Imports System.Net
Imports Newtonsoft.Json.Linq
Public Class Form1

    Private Sub Form1_Load(sender As Object, e As EventArgs) Handles Me.Load
        'set SecurityProtocolType to Tls12 before consume the API
        ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12
    End Sub

    Private Async Sub BtnBindAsync_Click(sender As Object, e As EventArgs) Handles BtnBindAsync.Click

        'get the market list
        Dim listMarket As List(Of String) = Await GetMarketPairsAsync

        'bind the list to ListBox
        ListBox1.DataSource = listMarket

        'bind the list to ComboBox
        ComboBox1.DataSource = listMarket

        'convert the list to DataTable, then bind to DataGridView using DataSource Property
        Dim DT As New DataTable
        DT.Columns.Add("Pairs", GetType(String))
        For Each x In listMarket
            DT.Rows.Add(x)
        Next
        DataGridView1.DataSource = DT

        'get last value of BTC/IDR pair
        Dim lastBTC As Double = Await GetLastBTCAsync
        MsgBox(lastBTC)

    End Sub

End Class
```
</details>

---
### Example Request Synchronous Operation

##### `Function GetResponseSync(address As String)`
<details>
<summary> Click To expand</summary>
    
```vb
    Private Function GetResponseSync(address As String) As String
    'declare variable for output
    Dim result As String = Nothing

    ' Create a request for the URL. 
    Dim request As WebRequest = WebRequest.Create(address)
    ' Set the Method property of the request to GET. 
    request.Method = "GET"
    ' Get the response.  
    Using response As WebResponse = request.GetResponse()
        ' Display the status.  
        'Example: OK
        Console.WriteLine(CType(response, HttpWebResponse).StatusDescription)

        ' Get the stream containing content returned by the server. 
        Using dataStream As Stream = response.GetResponseStream()
            ' Open the stream using a StreamReader for easy access.  
            Using reader As New StreamReader(dataStream)
                ' Read the content.  
                result = reader.ReadToEnd()
            End Using
        End Using
    End Using

    'return the output
    Return result

End Function
```
</details>

##### `Function GetMarketPairsSync`
<details>
<summary> Click To expand</summary>
    
```vb
    Public Function GetMarketPairsSync() As List(Of String)

    'get JSON String
    Dim JsonString As String = GetResponseSync("https://indodax.com/api/pairs")

    'parse raw JSON string as JArray
    Dim JsonArray As JArray = JArray.Parse(JsonString)

    'linq create list of string
    Dim result As List(Of String) = (From x As JToken In JsonArray
                                     Select x("description").ToObject(Of String)).ToList

    Return result
End Function
```
</details>

##### `Function GetLastBTCSync`
<details>
<summary> Click To expand</summary>
    
```vb
    Public Function GetLastBTCSync() As Double

    'get JSON String
    Dim JsonString As String = GetResponseSync("https://indodax.com/api/ticker/btcidr")

    'parse raw JSON string as JObject
    Dim JsonObj As JObject = JObject.Parse(JsonString)

    'linq create list of string
    Dim result As Double = JsonObj("ticker")("last")

    Return result
End Function

```
</details>

##### `Example Usage Sync Operation`
<details>
<summary> Click To expand</summary>
    
```vb
Imports System.IO
Imports System.Net
Imports Newtonsoft.Json.Linq

Public Class Form1

    Private Sub Form1_Load(sender As Object, e As EventArgs) Handles Me.Load
        'set SecurityProtocolType to Tls12 before consume the API
        ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12
    End Sub

    Private Sub BtnBindSync_Click(sender As Object, e As EventArgs) Handles BtnBindSync.Click

        'get the market list
        Dim listMarket As List(Of String) = GetMarketPairsSync

        'bind the list to ListBox
        ListBox1.DataSource = listMarket

        'example bind the list to ComboBox
        ComboBox1.DataSource = listMarket

        'convert the list to DataTable, then bind to DataGridView using DataSource Property
        Dim DT As New DataTable
        DT.Columns.Add("Pairs", GetType(String))
        For Each x In listMarket
            DT.Rows.Add(x)
        Next
        DataGridView1.DataSource = DT

        'get last value of BTC/IDR pair
        Dim lastBTC As Double = CIndodax.GetLastBTCSync
        MsgBox(lastBTC)

    End Sub

End Class
```
</details>

---
### Full code With Class helper
<details>
<summary> Click To expand</summary>
    
```vb
Imports System.IO
Imports System.Net
Imports Newtonsoft.Json.Linq

Public Class Form1

    Private Sub Form1_Load(sender As Object, e As EventArgs) Handles Me.Load
        'set SecurityProtocolType to Tls12 before consume the API
        ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12
    End Sub

    'synchronous
    Private Sub BtnBindSync_Click(sender As Object, e As EventArgs) Handles BtnBindSync.Click

        'declare new instance of CIndodax class
        Dim cIndodax As New CIndodax

        'get the market list
        Dim listMarket As List(Of String) = cIndodax.GetMarketPairsSync

        'bind the list to ListBox
        ListBox1.DataSource = listMarket
        'bind the list to ComboBox
        ComboBox1.DataSource = listMarket

        'convert the list to DataTable, then bind to DataGridView using DataSource Property
        Dim DT As New DataTable
        DT.Columns.Add("Pairs", GetType(String))
        For Each x In listMarket
            DT.Rows.Add(x)
        Next
        DataGridView1.DataSource = DT

        'get last value of BTC/IDR pair
        Dim lastBTC As Double = cIndodax.GetLastBTCSync
        MsgBox(lastBTC)

    End Sub

    'asynchronous
    Private Async Sub BtnBindAsync_Click(sender As Object, e As EventArgs) Handles BtnBindAsync.Click

        'declare new instance of CIndodax class
        Dim cIndodax As New CIndodax

        'get the market list
        Dim listMarket As List(Of String) = Await cIndodax.GetMarketPairsAsync

        'bind the list to ListBox
        ListBox1.DataSource = listMarket
        'bind the list to ComboBox
        ComboBox1.DataSource = listMarket

        'convert the list to DataTable, then bind to DataGridView using DataSource Property
        Dim DT As New DataTable
        DT.Columns.Add("Pairs", GetType(String))
        For Each x In listMarket
            DT.Rows.Add(x)
        Next
        DataGridView1.DataSource = DT

        'get last value of BTC/IDR pair
        Dim lastBTC As Double = Await cIndodax.GetLastBTCAsync
        MsgBox(lastBTC)

    End Sub

End Class

Public Class CIndodax
#Region "asynchronous function"
    Private Async Function GetResponseAsync(address As String) As Task(Of String)
        'declare variable for output
        Dim result As String = Nothing

        ' Create a request for the URL. 
        Dim request As WebRequest = WebRequest.Create(address)
        ' Set the Method property of the request to GET. 
        request.Method = "GET"
        ' Get the response.  
        Using response As WebResponse = Await request.GetResponseAsync()
            ' Display the status.  
            'Example: OK
            Console.WriteLine(CType(response, HttpWebResponse).StatusDescription)

            ' Get the stream containing content returned by the server. 
            Using dataStream As Stream = response.GetResponseStream()
                ' Open the stream using a StreamReader for easy access.  
                Using reader As New StreamReader(dataStream)
                    ' Read the content.  
                    result = Await reader.ReadToEndAsync()
                End Using
            End Using
        End Using

        'return the output
        Return result

    End Function

    Public Async Function GetMarketPairsAsync() As Task(Of List(Of String))

        'get JSON String
        Dim JsonString As String = Await GetResponseAsync("https://indodax.com/api/pairs")

        'parse raw JSON string as JArray
        Dim JsonArray As JArray = JArray.Parse(JsonString)

        'linq create list of string
        Dim result As List(Of String) = (From x As JToken In JsonArray
                                         Select x("description").ToObject(Of String)).ToList

        Return result

    End Function

    Public Async Function GetLastBTCAsync() As Task(Of Double)

        'get JSON String
        Dim JsonString As String = Await GetResponseAsync("https://indodax.com/api/ticker/btcidr")

        'parse raw JSON string as JObject
        Dim JsonObj As JObject = JObject.Parse(JsonString)

        'linq create list of string
        Dim result As Double = JsonObj("ticker")("last")

        Return result
    End Function
#End Region

#Region "synchronous function"
    Private Function GetResponseSync(address As String) As String
        'declare variable for output
        Dim result As String = Nothing

        ' Create a request for the URL. 
        Dim request As WebRequest = WebRequest.Create(address)
        ' Set the Method property of the request to GET. 
        request.Method = "GET"
        ' Get the response.  
        Using response As WebResponse = request.GetResponse()
            ' Display the status.  
            'Example: OK
            Console.WriteLine(CType(response, HttpWebResponse).StatusDescription)

            ' Get the stream containing content returned by the server. 
            Using dataStream As Stream = response.GetResponseStream()
                ' Open the stream using a StreamReader for easy access.  
                Using reader As New StreamReader(dataStream)
                    ' Read the content.  
                    result = reader.ReadToEnd()
                End Using
            End Using
        End Using

        'return the output
        Return result

    End Function

    Public Function GetMarketPairsSync() As List(Of String)

        'get JSON String
        Dim JsonString As String = GetResponseSync("https://indodax.com/api/pairs")

        'parse raw JSON string as JArray
        Dim JsonArray As JArray = JArray.Parse(JsonString)

        'linq create list of string
        Dim result As List(Of String) = (From x As JToken In JsonArray
                                         Select x("description").ToObject(Of String)).ToList

        Return result
    End Function

    Public Function GetLastBTCSync() As Double

        'get JSON String
        Dim JsonString As String = GetResponseSync("https://indodax.com/api/ticker/btcidr")

        'parse raw JSON string as JObject
        Dim JsonObj As JObject = JObject.Parse(JsonString)

        'linq create list of string
        Dim result As Double = JsonObj("ticker")("last")

        Return result
    End Function
#End Region
End Class
```
</details>


