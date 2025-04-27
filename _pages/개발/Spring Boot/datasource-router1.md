---
title: "고가용성(High Availability) 보장을 위한 Datasource Router 적용 (1)"
date: "2025-04-14"
thumbnail: "/assets/img/thumbnail/datasource.png"
---

# 고가용성(High Availability) 보장을 위한 Datasource Router 적용 (1)
---

```java
public class RoutingDataSource extends AbstractRoutingDataSource {
  private final HaConfig haConfig;

  public RoutingDataSource(final HaConfig haConfig) {
     this.haConfig = haConfig;
  }

  @Override
  protected Object determineCurrentLookupKey() {
     if ( ServerType.SERVER_MASTER == haConfig.getActiveServerType() ) {
        return "master";
     } else {
        return "slave";
     }
  }
}
```