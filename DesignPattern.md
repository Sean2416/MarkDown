

## 簡單工廠模式(Simple Factory Pattern)

### 定義

- 透過建立一個類別來負責創建實體，這些實體都繼承於同一個父類別
- Product: 抽象的父類別(披薩)
- Factory: 負責建立實體邏輯的類別
- ConcreteProduct: 實體本身(海鮮、夏威夷)

### 優點

1. 工廠含必要的邏輯判斷，可以決定在什麼時候創建哪一個產品的實例，客戶端可以免除直接創建產品對象的責任。
2. 可以減少使用者的記憶量。
3. 可以在不修改客戶端資料的情況下，變更或新增具體產品，提高了系統的靈活性。

### 缺點

1. 工廠集中所有產品的創建邏輯，一旦無法正常使用，整個系統都受影響。
2. 系統擴展困難，一旦增加新產品就不得不修改工廠邏輯，而在產品多的情況下，可能造成工廠邏輯複雜，不利於系統的擴展與維護。

### 範例

1. 定義抽象類別

   - 披薩製作的程序包含AddMaterial(加料)、Bake(烘烤)，當客人要一個海鮮披薩時如下

     ```C#
     public SeaPizza MakePizza()
     {
         SeaPizza pizza = new SeaPizza();
         pizza.AddMaterial(); 
         pizza.Bake(); 
         return pizza;
     }
     ```

   -  當今天可人要改點夏威夷披薩時

     ```csharp
     public HawaiiPizza MakePizza()
     {
         HawaiiPizza pizza = new HawaiiPizza();
         pizza.AddMaterial(); 
         pizza.Bake(); 
         return pizza;
     }
     ```

   - 抽出通用方法，建立抽象類別

     ```cs
     public interface IPizza
     {
         void AddMaterial();
         void Bake();
     }
     ```

   - 實作各項類別

     ```cs
     public class SeaPizza: IPizza
     {
       public void AddMaterial()
       {
        Console.WriteLine("加入海鮮佐料");
       }
     
       public void Bake()
       {
        Console.WriteLine("烘烤海鮮披薩");
       }
     }
     
     public class HawaiiPizza : IPizza
     {
       public void AddMaterial()
       {
        Console.WriteLine("加入火腿片、鳳梨");
       }
     
       public void Bake()
       {
        Console.WriteLine("烘烤夏威夷披薩");
       }
     }
     ```

      

2. 實作工廠邏輯。

   ```cs
   public class SimpleFactory
   {
       public IPizza CreatePizza(string name)
       {
           if (name== "Sea")
               return new SeaPizza();
   
           if (name== "Hawaii")
               return new HawaiiPizza();
   
           else
               return null;
       }
   }
   ```

3. 封裝Pizza製成。

   ```cs
   public class PizzaStore
   {
       private SimpleFactory  _factory;
   
       public PizzaStore(SimpleFactory  pFactory)
       {
           _factory = pFactory;
       }
   
       public IPizza CreateProduct(string name)
       {
           IPizza pizza = _factory.CreatePizza(name);
           pizza.AddMaterial();
           pizza.Bake();
   
           return pizza ;
       }
   }
   ```

   

   ```cs
   static void Main(string[] args)
   {
       PizzaStore store = new PizzaStore(new SimpleFactory());
   
       store.CreateProduct("Sea");
   
       store.CreateProduct("Hawaii");
   }
   ```































