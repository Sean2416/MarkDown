

# [Unit Of Work](https://aspnetboilerplate.com/Pages/Documents/Unit-Of-Work) (Transaction Scope)

- ###### ABP 透過**UOW** 來管理資料庫的**連線** 及 **Transaction**

  - ###### 進入UOW後開啟資料庫連線及Transaction

  - ###### 離開UOW後關閉連線及完成Transaction

  - ###### 中間若產生 **Exception**則關閉連線並 **rolled back**

- ###### 在一次`UOW`中如果發生**Exception**的情形則系統自動將先前的交易進行 **rolled back**，還原回資料修改前的樣子

  - ###### 以下圖為例，再新增User的API中可以看到系統執行`UserMgr.CreateAsync`且得到成功的訊息。但在其後因為系統故障而跳出Exception後，資料自動進行 **rolled back**並沒有將資料存入資料庫中。

  - ###### 因此，我們可以理解到當Server接受到Request並進入Application Service的方法時即開啟UOW，並在資料正確Response後才會將資料Commit並關閉資料庫連線

  - ![image-20210614134859950](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210614134859950.png)

  - ![image-20210614135203436](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210614135203436.png)

  - ![image-20210614135500873](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210614135500873.png)

  

## Enable Uow

- ###### 透過在Method上加入 `[UnitOfWork]`即可讓該方法啟用UOW

  - ```C#
    [UnitOfWork]
    public void CreatePerson(CreatePersonInput input)
    {
        var person = new Person { Name = input.Name, EmailAddress = input.EmailAddress };
        _personRepository.Insert(person);
        _statisticsRepository.IncrementPeopleCount();
    }
    ```

- ###### 或是透過 `IUnitOfWorkManager`一樣可以達到該效果

  - 透過 `IUnitOfWorkManager` 可以自己定義開始及結束的範圍

  - `.Begin()`開啟一個UOW，並透過 `.Complete()`結束

  - 如果沒有執行`.Complete()`，在程式結束後所有交易會被 **rolled back**

  - ```C#
    public class MyService
    {
        private readonly IUnitOfWorkManager _unitOfWorkManager;
        private readonly IPersonRepository _personRepository;
        private readonly IStatisticsRepository _statisticsRepository;
    
        public MyService(IUnitOfWorkManager unitOfWorkManager, IPersonRepository personRepository, IStatisticsRepository statisticsRepository)
        {
            _unitOfWorkManager = unitOfWorkManager;
            _personRepository = personRepository;
            _statisticsRepository = statisticsRepository;
        }
    
        public void CreatePerson(CreatePersonInput input)
        {
            var person = new Person { Name = input.Name, EmailAddress = input.EmailAddress };
    
            using (var unitOfWork = _unitOfWorkManager.Begin())
            {
                _personRepository.Insert(person);
                _statisticsRepository.IncrementPeopleCount();
    
                unitOfWork.Complete();
            }
        }
    }
    ```

  - ###### 要注意的是，必須執行完` unitOfWork.Complete()`資料才會進行Commit

  - ![image-20210614141858383](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210614141858383.png)

  - ![image-20210614141937883](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210614141937883.png)

## Disabled Uow

- ###### 透過在Method上加入 `[UnitOfWork(IsDisabled = true)]`即可關閉UOW的Scope

  - ###### 從下面範例可以看出，雖然系統在Response前拋出了exception。但是由於關閉UOW的緣故，因此資料依然在執行`UserMgr.CreateAsync`後就寫入資料庫中

  - ![image-20210614140514627](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210614140514627.png)

  - ![image-20210614140539866](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210614140539866.png)



## UOW Scope

- ###### 在ABP中，每一個Application Service都自動包覆一層UOW。也就是說，每一次的API Request就代表一次的 **Transaction**。

- ###### Transaction會在進入第一個Uow 方法時開啟，並在該方法完成後Commit。其間若內層的涵式有另外宣告Uow的話，此時會以最先開啟的Uow為主。

  - ###### 以下圖為例，Uow1為第一個開啟Connection的方法。因此，雖然Uow2已經完成交易，但因為Uow1發生Exception無法正確Commit資料，因此整個交易依然被rolled back

  - ![image-20210614142441393](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210614142441393.png)

  - ![image-20210614142747421](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210614142747421.png)

  - ![image-20210614142849790](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210614142849790.png)

- ###### 如果是在不同Scope內的Uow，則不會影響到彼此的Transaction

  - ![image-20210614143141550](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210614143141550.png)
  - ![image-20210614143152474](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210614143152474.png)

## Automatically Saving Changes

- ###### 在Uow內透過資料庫取得出來的資料，在程式裡面有值的變更時。即使沒有Update資料庫，在Uow結束後也會自動更新

  - ###### 以下圖為例，當我們變更從資料庫取得的User資料欄位`Name`時，我們可以發現下面的涵式並未執行Update的指令。但回到資料庫查詢該筆資料時可以發現，`Name`欄位已經變更完成。

  - ![image-20210614173921835](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210614173921835.png)

  - ![image-20210614174000156](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210614174000156.png)



# [Data Filters](https://aspnetboilerplate.com/Pages/Documents/Data-Filters)

## 1. SoftDelete

- ###### 針對資料庫中某些資料表在進行刪除時，不會真的把資料刪掉而是透過註記的方式讓資料不會在Search時備查找出來。

- ###### ABP中提供 `IsDeleted `欄位作為標註那些不會真的刪除資料的資料表，只要繼承`ISoftDelete`的Entity都會被視為要進行Soft Delete的資料表

  - ```C#
       public class Person : Entity, ISoftDelete
        {
            public virtual string Name { get; set; }
        
            public virtual bool IsDeleted { get; set; }
        }
    ```

- ###### 使用**ISoftDelete** 的資料表在刪除時不會真的將資料刪掉，而是透過變更 `IsDeleted = true`的方式註記為刪除。

  - ###### 從下面範例中可以看出，`TestUowUser1`並沒有從資料庫中實際刪除而是變更了`IsDeleted `欄位值

  - ![image-20210615144415459](C:\Users\6591\AppData\Roaming\Typora\typora-user-images\image-20210615144415459.png)

  - ![image-20210615144449618](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210615144449618.png)

- ###### 針對該表Select資料時，ABP預設會將 `IsDeleted = true`的資料過濾掉

  - ###### 可以看出搜尋回來的資料內，已經過濾掉TestUowUser1

  - ![image-20210615144701015](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210615144701015.png)

## 2. Multi-Tenant

- ###### ABP 內建提供多組織架構，資料表內 加入`IMayHaveTenant` 、`IMustHaveTenant` 就能在查找資料時自動過濾組織資料。

  - ###### 在 `IAbpSession`中有一個 `TenantId`的值，預設取得Claims中的`AbpClaimTypes.TenantId`值。

  - ###### 如果開啟多組織架構時資料表會自動根據TenantId去做資料篩選。(例如，登入者Session內TenantId取得為`組織編號3`，則有使用多組織註記的資料表會自動篩選編號為3的內容出來)

  - ###### 多組織標記

    - ######  IMustHaveTenant: 加入此註記的資料表會自動產生`TenantId`欄位，且該欄位為必填。並且在新增資料時會自動根據登入者的TenantId塞入欄位資料。

    - ###### IMayHaveTenant: 加入此註記的資料表會自動產生`TenantId`欄位，該欄位為非必填。在進行資料新增時，必須主動給予TenantId欄位數值。

    - ###### 兩則標記共通處在於查詢資料時，只要有開啟多組織標記則資料表會自動根據登入者TenantId篩選資料

    - ```C#
      Configuration.MultiTenancy.IsEnabled = true; //在Module內加入開啟多組織架構
      ```

    - ###### 沒有使用多組織標記的資料表不在此限中

## 3. Disable Filters

- ###### 透過`DisableFilter ` 可以關閉特定的過濾條件(SoftDelete、MultiTenant)。

  - ######  可以看倒，透過DisableFilter後查詢出來的結果已經包含了被刪除的資料列

  - ![image-20210615152427880](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210615152427880.png)



# [Specifications](https://aspnetboilerplate.com/Pages/Documents/Specifications)



# [Entities](https://aspnetboilerplate.com/Pages/Documents/Entities)



# [Multi-Lingual Entities](https://aspnetboilerplate.com/Pages/Documents/Multi-Lingual-Entities)



# [Value Objects](https://aspnetboilerplate.com/Pages/Documents/Value-Objects)



# [Repositories](https://aspnetboilerplate.com/Pages/Documents/Repositories)



# [Domain Services](https://aspnetboilerplate.com/Pages/Documents/Domain-Services)



# [Domain Events (EventBus)](https://aspnetboilerplate.com/Pages/Documents/EventBus-Domain-Events)



# [Dynamic Parameter System](https://aspnetboilerplate.com/Pages/Documents/Dynamic-Parameter-System)



# [Object Comparators](https://aspnetboilerplate.com/Pages/Documents/ObjectComparators)