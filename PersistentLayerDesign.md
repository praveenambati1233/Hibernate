
**Persistent Data Layer **


![](https://raw.githubusercontent.com/praveenambati1233/Hibernate/master/DataLayer.PNG)

```java
OrderDataSourceConfig orderSrcConfig = this.ordDataDao.findBySvcTypeAndFlowType(svcType, FlowType.Site);
orderSrcConfig.getOrderDataSource();
```

For every persistent layer below would be design 

```java

@Entity
@Table(name = "ORDER_DATA_SOURCE_CONFIG")
OrderDataSourceConfig  ( Table ) 

OrderDataSourceConfigDao ( Interface )
    boolean isInvalidServiceType(ServiceType svcType, FlowType flowType);
    boolean isGODServiceType(final ServiceType svcType, final FlowType flowType);
    OrderDataSourceConfig findBySvcTypeAndFlowType(ServiceType svcType, FlowType flowType);

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
