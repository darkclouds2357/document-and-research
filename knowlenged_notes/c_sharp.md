**Garbage Collection (GC)** trong C# là một cơ chế tự động quản lý bộ nhớ, giúp giải phóng bộ nhớ không còn được sử dụng bởi các đối tượng không còn tham chiếu trong chương trình. C# sử dụng Garbage Collector (GC) để thu gom rác (garbage) và ngăn chặn hiện tượng rò rỉ bộ nhớ (memory leak), giúp quản lý tài nguyên hiệu quả.

### Nguyên lý hoạt động của GC:
GC trong C# hoạt động dựa trên ba nguyên tắc chính:

1. **Generation (Thế hệ):**
   - Bộ nhớ trong C# được chia thành ba thế hệ (Generations) để tối ưu hóa quá trình thu gom:
     - **Generation 0:** Chứa các đối tượng mới được tạo. Những đối tượng thường có tuổi đời ngắn và có khả năng sẽ bị thu gom ngay trong lần chạy GC đầu tiên.
     - **Generation 1:** Chứa các đối tượng sống sót qua ít nhất một lần GC của Generation 0. Đây là vùng bộ nhớ trung gian cho các đối tượng.
     - **Generation 2:** Chứa các đối tượng sống sót qua nhiều lần GC và thường là các đối tượng tồn tại trong suốt vòng đời của ứng dụng (long-lived objects).
   - GC ưu tiên thu gom ở các thế hệ thấp hơn trước (Generation 0) vì khả năng đối tượng còn tồn tại ở đó thấp hơn. Nếu một đối tượng sống sót qua nhiều lần GC, nó sẽ được di chuyển lên thế hệ cao hơn (Generation 1, 2).

2. **Mark and Sweep (Đánh dấu và Quét):**
   - **Marking:** GC sẽ đánh dấu tất cả các đối tượng có thể truy cập từ các "gốc" (roots) của chương trình, chẳng hạn như biến cục bộ, biến tĩnh, hoặc các đối tượng đang được tham chiếu.
   - **Sweeping:** Sau khi đánh dấu, GC sẽ quét qua bộ nhớ để thu gom các đối tượng không được đánh dấu (nghĩa là không còn tham chiếu nào đến chúng), và giải phóng bộ nhớ mà chúng chiếm dụng.

3. **Compacting (Nén bộ nhớ):**
   - Sau khi thu gom, GC có thể nén bộ nhớ bằng cách di chuyển các đối tượng sống sót đến một vùng liên tiếp trong bộ nhớ. Điều này giảm thiểu phân mảnh bộ nhớ và tăng hiệu suất truy cập dữ liệu.

### Các pha của Garbage Collection:

1. **Initial Mark (Đánh dấu ban đầu):** GC dừng mọi luồng (threads) và đánh dấu các đối tượng đang được tham chiếu trực tiếp từ các gốc.
2. **Concurrent Mark (Đánh dấu đồng thời):** GC tiếp tục đánh dấu các đối tượng có thể truy cập trong khi ứng dụng vẫn chạy.
3. **Sweep (Quét):** GC giải phóng bộ nhớ của các đối tượng không được đánh dấu.
4. **Compaction (Nén):** Nếu cần, GC sẽ nén bộ nhớ, di chuyển các đối tượng còn sống sót để giảm phân mảnh.

### Các loại Garbage Collection trong C#:

1. **Workstation GC:**
   - Được sử dụng cho các ứng dụng máy tính để bàn (desktop). Workstation GC có hai chế độ:
     - **Concurrent GC:** GC chạy đồng thời với ứng dụng, giảm thiểu việc tạm dừng (pausing) ứng dụng.
     - **Non-concurrent GC:** GC tạm dừng ứng dụng khi thực hiện thu gom.

2. **Server GC:**
   - Được tối ưu hóa cho các ứng dụng máy chủ (server) với nhiều bộ xử lý (processors). Server GC chạy song song trên các bộ xử lý khác nhau để tăng hiệu suất.

3. **Background GC:**
   - Là một phần của Workstation GC và Server GC, giúp giảm thiểu thời gian tạm dừng của ứng dụng bằng cách thực hiện thu gom nền (background collection) trong khi ứng dụng vẫn tiếp tục chạy.

### Cách thức kích hoạt Garbage Collection:
- GC tự động kích hoạt khi bộ nhớ sắp hết, khi chương trình yêu cầu bộ nhớ, hoặc khi nó xác định rằng việc thu gom có thể cải thiện hiệu suất.
- Tuy nhiên, bạn có thể ép GC chạy thủ công bằng cách sử dụng phương thức `GC.Collect()`. Tuy nhiên, cần thận trọng khi sử dụng vì nó có thể gây ra gián đoạn không cần thiết cho ứng dụng.

### Một số lưu ý khi làm việc với GC trong C#:
- **Dispose pattern:** Đối với các tài nguyên không được quản lý (unmanaged resources) như kết nối mạng, file, hoặc kết nối cơ sở dữ liệu, cần sử dụng giao diện `IDisposable` và phương thức `Dispose()` để giải phóng tài nguyên một cách đúng cách.
- **Finalize:** Phương thức `Finalize` được gọi bởi GC trước khi đối tượng bị thu gom. Tuy nhiên, việc sử dụng `Finalize` có thể làm chậm quá trình thu gom, do đó chỉ nên sử dụng khi cần thiết.
- **Weak References:** Để giữ tham chiếu đến một đối tượng mà không ngăn cản GC thu gom đối tượng đó, bạn có thể sử dụng `WeakReference`.

### Kết luận:
Garbage Collection trong C# là một công cụ mạnh mẽ giúp quản lý bộ nhớ một cách tự động, nhưng hiểu rõ cách thức hoạt động của nó sẽ giúp bạn viết mã hiệu quả hơn và tránh các vấn đề liên quan đến quản lý tài nguyên trong ứng dụng của mình.

--------------

Khi làm việc với đa luồng (multithreading) trong C#, có một số điểm quan trọng và lưu ý mà bạn cần cân nhắc để đảm bảo hiệu suất, an toàn và tính ổn định của ứng dụng. Dưới đây là các chú ý chính khi làm việc với đa luồng trong C#:

### **1. Hiểu Về Threading**

- **Thread:** Là đơn vị cơ bản của thực thi đồng thời trong .NET. Mỗi thread có thể thực hiện một phần công việc của ứng dụng đồng thời với các threads khác.
- **Task:** Là một lớp cao cấp hơn so với thread, được sử dụng trong `System.Threading.Tasks`. Task cung cấp một cách đơn giản hơn để thực hiện các công việc đồng thời mà không cần quản lý các thread trực tiếp.

### **2. Đảm Bảo An Toàn Đa Luồng (Thread Safety)**

- **Synchronization:** Khi nhiều threads truy cập và thay đổi cùng một dữ liệu, bạn cần phải sử dụng cơ chế đồng bộ hóa để tránh tình trạng dữ liệu bị hỏng hoặc xung đột. Các cơ chế đồng bộ hóa bao gồm `lock`, `Monitor`, `Mutex`, `Semaphore`, và `ReaderWriterLockSlim`.
  ```csharp
  private readonly object _lockObject = new object();
  
  public void SafeMethod()
  {
      lock (_lockObject)
      {
          // Code that accesses shared resources
      }
  }
  ```

- **Concurrent Collections:** Sử dụng các collection đồng bộ như `ConcurrentDictionary`, `ConcurrentQueue`, `ConcurrentBag` thay vì các collection thông thường để đảm bảo an toàn khi truy cập đồng thời.

### **3. Tránh Deadlock**

- **Deadlock:** Xảy ra khi hai hoặc nhiều threads chờ đợi nhau giải phóng tài nguyên, dẫn đến tình trạng ứng dụng bị treo. Để tránh deadlock:
  - **Sử Dụng Lock Hierarchy:** Đặt quy tắc để lấy các locks theo thứ tự cố định.
  - **Tránh Nested Locks:** Hạn chế việc sử dụng locks lồng nhau hoặc sử dụng các locks nhỏ hơn và không lồng nhau.

### **4. Sử Dụng Task và Async/Await**

- **Task:** Sử dụng `Task` và `Task<T>` từ thư viện `System.Threading.Tasks` để thực hiện các công việc đồng thời một cách dễ dàng và hiệu quả hơn so với việc trực tiếp quản lý threads.
  ```csharp
  Task.Run(() => 
  {
      // Do some work asynchronously
  });
  ```

- **Async/Await:** Sử dụng `async` và `await` để viết mã đồng thời dễ đọc và bảo trì hơn. Chúng giúp đơn giản hóa việc quản lý luồng và đồng bộ hóa các hoạt động không đồng bộ.
  ```csharp
  public async Task<string> FetchDataAsync()
  {
      HttpClient client = new HttpClient();
      string result = await client.GetStringAsync("http://example.com");
      return result;
  }
  ```

### **5. Hiệu Suất và Tối Ưu**

- **Thread Pool:** Sử dụng `ThreadPool` để quản lý và tái sử dụng threads hiệu quả. `ThreadPool` tự động xử lý việc tạo và quản lý threads, giảm overhead so với việc tạo các thread mới.
  ```csharp
  ThreadPool.QueueUserWorkItem(state =>
  {
      // Do some work
  });
  ```

- **Avoid Blocking Calls:** Hạn chế các gọi blocking trên các thread hoặc task, vì điều này có thể làm giảm hiệu suất tổng thể của ứng dụng. Thay vào đó, sử dụng các phương pháp không đồng bộ và không blocking.

### **6. Thực Thi và Quản Lý**

- **Cancellation Tokens:** Sử dụng `CancellationToken` để cho phép hủy bỏ các task hoặc operation khi không còn cần thiết hoặc khi có lỗi xảy ra.
  ```csharp
  public async Task LongRunningOperationAsync(CancellationToken cancellationToken)
  {
      // Use cancellationToken.ThrowIfCancellationRequested() to check for cancellation
  }
  ```

- **Exception Handling:** Xử lý các ngoại lệ xảy ra trong các thread hoặc task. Sử dụng `try-catch` để bắt và xử lý lỗi, và đảm bảo rằng các tài nguyên được giải phóng đúng cách.
  ```csharp
  try
  {
      await Task.Run(() =>
      {
          // Code that may throw exception
      });
  }
  catch (Exception ex)
  {
      // Handle exception
  }
  ```

### **7. Debugging và Testing**

- **Debugging:** Debugging các ứng dụng đa luồng có thể khó khăn hơn. Sử dụng các công cụ như Visual Studio Debugger để theo dõi và phân tích các threads và tasks.
- **Testing:** Viết các bài kiểm tra để đảm bảo rằng các phần của mã hoạt động đúng trong các tình huống đồng thời và đảm bảo không có lỗi liên quan đến đồng bộ hóa.

### **8. Các Thư Viện và Frameworks Hữu Ích**

- **TPL (Task Parallel Library):** Cung cấp các công cụ cho việc lập trình đồng thời và song song.
- **PLINQ (Parallel LINQ):** Cho phép thực hiện các truy vấn LINQ song song.
- **Concurrent Collections:** Bao gồm các lớp như `ConcurrentDictionary`, `ConcurrentQueue`, và `ConcurrentBag` giúp xử lý các vấn đề đồng bộ hóa dữ liệu.

### **Kết Luận**

Khi làm việc với đa luồng trong C#, bạn cần phải chú ý đến các vấn đề an toàn đa luồng, hiệu suất, và quản lý. Việc sử dụng các công cụ và kỹ thuật phù hợp sẽ giúp bạn xây dựng các ứng dụng đồng thời hiệu quả và ổn định.

--------------

`ConcurrentDictionary` là một lớp trong .NET thuộc không gian tên `System.Collections.Concurrent`, cung cấp một dictionary đồng bộ hóa cho phép nhiều luồng (threads) truy cập và thao tác đồng thời mà không cần phải sử dụng cơ chế đồng bộ hóa thủ công như `lock`. Đây là một phần của bộ sưu tập đồng thời (concurrent collections) được thiết kế để giải quyết các vấn đề liên quan đến truy cập đồng thời một cách an toàn và hiệu quả.

### **Các Tính Năng Của ConcurrentDictionary**

1. **Đồng Bộ Hóa Tự Động:**
   - `ConcurrentDictionary` tự động xử lý các vấn đề đồng bộ hóa khi nhiều luồng thực hiện các thao tác đồng thời trên dictionary, giảm bớt sự cần thiết phải sử dụng các khóa (`locks`) bên ngoài.

2. **Hiệu Suất Cao:**
   - Được thiết kế để hoạt động hiệu quả trong môi trường đa luồng, với các thuật toán tối ưu hóa nhằm giảm thiểu tranh chấp (contention) giữa các luồng.

3. **Các Phương Thức Thao Tác An Toàn:**
   - Cung cấp các phương thức cho phép bạn thực hiện các thao tác như thêm, xóa và cập nhật dữ liệu một cách an toàn khi nhiều luồng truy cập.

### **Các Phương Thức Chính Của ConcurrentDictionary**

1. **Thêm Dữ Liệu:**
   - **`TryAdd`**: Thêm một mục mới vào dictionary nếu khóa không tồn tại.
     ```csharp
     var dict = new ConcurrentDictionary<int, string>();
     bool added = dict.TryAdd(1, "value1");
     ```

2. **Cập Nhật Dữ Liệu:**
   - **`TryUpdate`**: Cập nhật giá trị của một khóa cụ thể nếu giá trị hiện tại khớp với giá trị mong đợi.
     ```csharp
     bool updated = dict.TryUpdate(1, "newValue1", "value1");
     ```

3. **Xóa Dữ Liệu:**
   - **`TryRemove`**: Xóa một mục khỏi dictionary nếu khóa tồn tại.
     ```csharp
     bool removed = dict.TryRemove(1, out string value);
     ```

4. **Lấy Dữ Liệu:**
   - **`TryGetValue`**: Lấy giá trị của một khóa cụ thể nếu khóa tồn tại.
     ```csharp
     bool exists = dict.TryGetValue(1, out string value);
     ```

5. **Xử Lý Dữ Liệu Tại Địa Phương:**
   - **`GetOrAdd`**: Lấy giá trị của khóa nếu nó tồn tại; nếu không, thêm giá trị mới và trả về nó.
     ```csharp
     string value = dict.GetOrAdd(1, key => "defaultValue");
     ```

   - **`AddOrUpdate`**: Cập nhật giá trị của khóa nếu khóa đã tồn tại hoặc thêm khóa với giá trị mới nếu khóa không tồn tại.
     ```csharp
     string updatedValue = dict.AddOrUpdate(1, key => "newValue", (key, oldValue) => "updatedValue");
     ```

6. **Đếm và Kiểm Tra Sự Tồn Tại:**
   - **`Count`**: Đếm số lượng mục trong dictionary.
     ```csharp
     int count = dict.Count;
     ```

   - **`ContainsKey`**: Kiểm tra xem một khóa có tồn tại trong dictionary không.
     ```csharp
     bool contains = dict.ContainsKey(1);
     ```

### **Ví Dụ Cụ Thể**

Dưới đây là một ví dụ đơn giản về cách sử dụng `ConcurrentDictionary`:

```csharp
using System;
using System.Collections.Concurrent;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        var concurrentDict = new ConcurrentDictionary<int, string>();

        // Thêm dữ liệu
        concurrentDict.TryAdd(1, "Value1");

        // Cập nhật dữ liệu
        concurrentDict.TryUpdate(1, "UpdatedValue1", "Value1");

        // Lấy dữ liệu
        string value = concurrentDict.GetOrAdd(2, key => "Value2");
        Console.WriteLine($"Value for key 2: {value}");

        // Xóa dữ liệu
        concurrentDict.TryRemove(1, out value);
        Console.WriteLine($"Removed value for key 1: {value}");

        // Xử lý đồng thời
        Parallel.For(0, 10, i =>
        {
            concurrentDict.TryAdd(i, $"Value{i}");
        });

        // In tất cả các giá trị
        foreach (var kvp in concurrentDict)
        {
            Console.WriteLine($"{kvp.Key}: {kvp.Value}");
        }
    }
}
```

### **Kết Luận**

`ConcurrentDictionary` là một công cụ mạnh mẽ trong .NET cho phép bạn xử lý các tình huống đồng thời mà không cần lo lắng về vấn đề đồng bộ hóa thủ công. Bằng cách cung cấp các phương thức an toàn và hiệu quả để thao tác với dữ liệu, nó giúp đơn giản hóa việc xây dựng ứng dụng đa luồng và nâng cao hiệu suất của ứng dụng.

-------------------

`ConcurrentQueue<T>` là một lớp trong .NET thuộc không gian tên `System.Collections.Concurrent`, cung cấp một hàng đợi (queue) đồng bộ hóa cho phép nhiều luồng (threads) thực hiện các thao tác đồng thời mà không cần phải sử dụng cơ chế đồng bộ hóa thủ công như `lock`. Đây là một phần của bộ sưu tập đồng thời (concurrent collections) được thiết kế để xử lý các vấn đề liên quan đến truy cập đồng thời một cách an toàn và hiệu quả.

### **Các Tính Năng Của ConcurrentQueue**

1. **Đồng Bộ Hóa Tự Động:**
   - `ConcurrentQueue` tự động xử lý đồng bộ hóa cho các thao tác thêm và lấy phần tử khỏi hàng đợi, giúp bạn tránh phải sử dụng các khóa (`locks`) bên ngoài.

2. **Hiệu Suất Cao:**
   - Được thiết kế để hoạt động hiệu quả trong môi trường đa luồng, với các thuật toán tối ưu hóa nhằm giảm thiểu tranh chấp (contention) giữa các luồng.

3. **FIFO (First In, First Out):**
   - Hàng đợi hoạt động theo nguyên tắc FIFO, nghĩa là phần tử đầu tiên được thêm vào hàng đợi sẽ là phần tử đầu tiên được lấy ra.

### **Các Phương Thức Chính Của ConcurrentQueue**

1. **Thêm Phần Tử:**
   - **`Enqueue`**: Thêm một phần tử vào cuối hàng đợi.
     ```csharp
     var queue = new ConcurrentQueue<int>();
     queue.Enqueue(1);
     queue.Enqueue(2);
     ```

2. **Lấy Phần Tử:**
   - **`TryDequeue`**: Lấy và loại bỏ phần tử khỏi đầu hàng đợi nếu hàng đợi không rỗng. Phương thức này trả về `true` nếu phần tử được lấy thành công, ngược lại trả về `false`.
     ```csharp
     if (queue.TryDequeue(out int result))
     {
         Console.WriteLine($"Dequeued: {result}");
     }
     ```

   - **`TryPeek`**: Lấy phần tử từ đầu hàng đợi mà không loại bỏ nó. Phương thức này trả về `true` nếu phần tử được lấy thành công, ngược lại trả về `false`.
     ```csharp
     if (queue.TryPeek(out int peekedResult))
     {
         Console.WriteLine($"Peeked: {peekedResult}");
     }
     ```

3. **Kiểm Tra Sự Tồn Tại:**
   - **`IsEmpty`**: Kiểm tra xem hàng đợi có rỗng hay không.
     ```csharp
     bool isEmpty = queue.IsEmpty;
     ```

### **Ví Dụ Cụ Thể**

Dưới đây là một ví dụ đơn giản về cách sử dụng `ConcurrentQueue`:

```csharp
using System;
using System.Collections.Concurrent;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        var queue = new ConcurrentQueue<int>();

        // Thêm phần tử vào hàng đợi
        queue.Enqueue(1);
        queue.Enqueue(2);

        // Lấy và loại bỏ phần tử
        if (queue.TryDequeue(out int dequeuedValue))
        {
            Console.WriteLine($"Dequeued value: {dequeuedValue}");
        }

        // Kiểm tra hàng đợi rỗng
        Console.WriteLine($"Is queue empty? {queue.IsEmpty}");

        // Xử lý đồng thời
        Parallel.For(0, 10, i =>
        {
            queue.Enqueue(i);
        });

        // In tất cả các giá trị trong hàng đợi
        while (queue.TryDequeue(out int value))
        {
            Console.WriteLine($"Value: {value}");
        }
    }
}
```

### **Ứng Dụng Thực Tế**

- **Xử Lý Đầu Vào/Đầu Ra (I/O):** Hàng đợi đồng bộ hóa có thể được sử dụng để xử lý các yêu cầu I/O đến từ nhiều nguồn khác nhau, giúp giảm tắc nghẽn và cải thiện hiệu suất.
- **Công Việc Nền Tảng (Background Work):** Sử dụng hàng đợi để lưu trữ và quản lý các công việc nền tảng cần thực hiện đồng thời, ví dụ như xử lý hình ảnh, gửi email, hoặc các công việc xử lý dữ liệu khác.
- **Tạo Hàng Đợi Tính Toán:** Khi cần thực hiện các phép toán tính toán hoặc các công việc phân tích đồng thời, `ConcurrentQueue` giúp quản lý các phép toán này một cách an toàn và hiệu quả.

### **Kết Luận**

`ConcurrentQueue<T>` là một công cụ mạnh mẽ trong .NET cho phép bạn xử lý các tình huống đồng thời với các thao tác hàng đợi mà không cần lo lắng về vấn đề đồng bộ hóa thủ công. Bằng cách cung cấp các phương thức an toàn và hiệu quả để thao tác với dữ liệu, nó giúp đơn giản hóa việc xây dựng ứng dụng đa luồng và nâng cao hiệu suất của ứng dụng.

----

Middleware trong .NET là một khái niệm quan trọng trong việc xử lý các yêu cầu HTTP trong các ứng dụng web. Nó là một phần của pipeline xử lý yêu cầu trong ứng dụng ASP.NET Core, cho phép bạn thực hiện các hành động cụ thể như xác thực, ghi log, xử lý lỗi, nén phản hồi, hoặc chỉnh sửa các yêu cầu và phản hồi trước khi chúng đến hoặc đi từ các endpoint của ứng dụng.

### **Cách Middleware Hoạt Động**

1. **Pipeline Xử Lý Yêu Cầu:**
   - Mỗi yêu cầu HTTP đi qua một chuỗi các middleware trong pipeline.
   - Mỗi middleware có thể thực hiện một số thao tác trên yêu cầu hoặc phản hồi, rồi chuyển tiếp yêu cầu đến middleware tiếp theo hoặc dừng pipeline.

2. **Đăng ký Middleware:**
   - Middleware được đăng ký trong phương thức `Configure` của `Startup.cs` trong ứng dụng ASP.NET Core. Middleware được đăng ký theo thứ tự xuất hiện, và thứ tự này rất quan trọng vì nó quyết định cách yêu cầu được xử lý qua các middleware.

3. **Kiến Trúc Mô-đun:**
   - Mỗi middleware thực hiện một chức năng cụ thể, giúp bạn dễ dàng tách biệt và quản lý các khía cạnh khác nhau của ứng dụng như xác thực, ủy quyền, logging, và xử lý lỗi.

### **Ví Dụ Middleware**

Dưới đây là một số ví dụ về middleware phổ biến trong ASP.NET Core:

1. **Logging Middleware:**
   - Ghi lại các yêu cầu và phản hồi để phục vụ mục đích giám sát và gỡ lỗi.

   ```csharp
   public class RequestLoggingMiddleware
   {
       private readonly RequestDelegate _next;
       private readonly ILogger<RequestLoggingMiddleware> _logger;

       public RequestLoggingMiddleware(RequestDelegate next, ILogger<RequestLoggingMiddleware> logger)
       {
           _next = next;
           _logger = logger;
       }

       public async Task InvokeAsync(HttpContext context)
       {
           _logger.LogInformation("Handling request: " + context.Request.Path);
           await _next(context);
           _logger.LogInformation("Finished handling request.");
       }
   }
   ```

   Đăng ký middleware trong `Startup.cs`:

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseMiddleware<RequestLoggingMiddleware>();
       // Other middleware registrations...
   }
   ```

2. **Authentication Middleware:**
   - Kiểm tra xem người dùng đã được xác thực hay chưa và xử lý logic liên quan đến xác thực.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAuthentication();
       // Other middleware registrations...
   }
   ```

3. **Exception Handling Middleware:**
   - Xử lý các ngoại lệ phát sinh trong quá trình xử lý yêu cầu và trả về phản hồi lỗi phù hợp.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
       }
       else
       {
           app.UseExceptionHandler("/Home/Error");
       }
   }
   ```

4. **Custom Middleware:**
   - Bạn cũng có thể tạo middleware tùy chỉnh để đáp ứng các nhu cầu cụ thể của ứng dụng. Ví dụ, middleware để thêm tiêu đề bảo mật vào tất cả các phản hồi HTTP.

   ```csharp
   public class SecurityHeadersMiddleware
   {
       private readonly RequestDelegate _next;

       public SecurityHeadersMiddleware(RequestDelegate next)
       {
           _next = next;
       }

       public async Task InvokeAsync(HttpContext context)
       {
           context.Response.Headers.Add("X-Content-Type-Options", "nosniff");
           await _next(context);
       }
   }

   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseMiddleware<SecurityHeadersMiddleware>();
   }
   ```

### **Cách Đăng Ký Middleware**

Middleware được đăng ký trong phương thức `Configure` của `Startup.cs` sử dụng các phương thức như `UseMiddleware<T>`, `Use`, hoặc các phương thức mở rộng sẵn có như `UseAuthentication`, `UseAuthorization`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Đăng ký các middleware
    app.UseMiddleware<CustomMiddleware>();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

### **Ưu Điểm của Middleware**

1. **Tính Modular:** Middleware cho phép chia ứng dụng thành các mô-đun nhỏ hơn, dễ quản lý và bảo trì.
2. **Tái Sử Dụng:** Middleware có thể dễ dàng tái sử dụng trong nhiều dự án hoặc chia sẻ giữa các ứng dụng.
3. **Kiểm Soát Pipeline:** Bạn có thể kiểm soát luồng xử lý của yêu cầu HTTP thông qua pipeline, cho phép linh hoạt trong việc quản lý các khía cạnh khác nhau của ứng dụng.
4. **Tích Hợp Dễ Dàng:** Middleware có thể dễ dàng tích hợp với các thư viện và dịch vụ khác, giúp mở rộng chức năng của ứng dụng mà không cần thay đổi kiến trúc cơ bản.

### **Kết Luận**

Middleware trong .NET là một công cụ mạnh mẽ cho phép bạn xử lý các yêu cầu và phản hồi HTTP một cách hiệu quả và linh hoạt. Bằng cách sử dụng và kết hợp các middleware khác nhau, bạn có thể tạo ra các pipeline xử lý yêu cầu tùy chỉnh, đáp ứng được nhu cầu cụ thể của ứng dụng.