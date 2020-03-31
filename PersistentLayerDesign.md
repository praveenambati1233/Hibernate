**Persistent Data Layer**



![](https://raw.githubusercontent.com/praveenambati1233/Hibernate/master/DataLayer.PNG)

For every persistent layer below would be design 

Create your model first and design your hibernate entity like below.

```java

@Entity
@Table(name = "ORDER_DATA_SOURCE_CONFIG")
OrderDataSourceConfig  ( Table ) 

```

create  Internface for CRUD operations 

```java
OrderDataSourceConfigDao ( Interface )
    boolean isInvalidServiceType(ServiceType svcType, FlowType flowType);
    boolean isGODServiceType(final ServiceType svcType, final FlowType flowType);
    OrderDataSourceConfig findBySvcTypeAndFlowType(ServiceType svcType, FlowType flowType);
```

Implement Dao interface and write the implementation logic.

```java

@Repository("OrderDataSourceConfigDao")
OrderDataSourceConfigDaoJpa implements OrderDataSourceConfigDao 
    @Override
    public boolean isInvalidServiceType(final ServiceType svcType, final FlowType flowType)
    {
        List<OrderDataSourceConfig> list = this.search("serviceType=? and flowType=? order by CREATED_DATE desc", svcType, flowType);
        
        return CollectionUtils.isEmpty(list) ? true : false;
    }
    @Override
    public boolean isGODServiceType(final ServiceType svcType, final FlowType flowType)
    {
        List<OrderDataSourceConfig> list = this.search("serviceType=? and flowType=? order by CREATED_DATE desc", svcType, flowType);
        
        if (!CollectionUtils.isEmpty(list) && OrderDataSourceEnum.getOrderData.equals(list.get(0).getOrderDataSource()))
        {
            return true;
        }

        return false;
    }
    @Override
    public OrderDataSourceConfig findBySvcTypeAndFlowType(final ServiceType svcType, final FlowType flowType)
    {
        List<OrderDataSourceConfig> list = this.search("serviceType=? and flowType=? order by CREATED_DATE desc", svcType, flowType);
        
        return CollectionUtils.isEmpty(list) ? null : list.get(0);
    }

```

Below snnipet code to call the DAO method

```java
OrderDataSourceConfig orderSrcConfig = this.ordDataDao.findBySvcTypeAndFlowType(svcType, FlowType.Site);
orderSrcConfig.getOrderDataSource();
```
