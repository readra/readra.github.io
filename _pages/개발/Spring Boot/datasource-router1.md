---
title: "고가용성(High Availability) 보장을 위한 Datasource Router 적용 (1)"
date: "2025-04-14"
thumbnail: "/assets/img/thumbnail/datasource.png"
---

# 고가용성(High Availability) 보장을 위한 Datasource Router 적용 (1)
---

```java
@Configuration
@EnableTransactionManagement
public class DataSourceConfig {
	// masterHikariConfig Bean 생략
	// slaveHikariConfig Bean 생략
	@Bean
	public DataSource masterDataSource() {
		return new HikariDataSource(masterHikariConfig());
	}

	@Bean
	public DataSource slaveCustomDataSource() {
		return new HikariDataSource(slaveHikariConfig());
	}

	@Bean
	public DataSource routingDataSource(@Qualifier("masterDataSource") DataSource masterDataSource, @Qualifier("slaveCustomDataSource") DataSource slaveDataSource) {
		RoutingDataSource routingDataSource = new RoutingDataSource(HaConfig);

		Map<Object, Object> targetDataSources = new HashMap<>();
		targetDataSources.put("master", masterDataSource);
		targetDataSources.put("slave", slaveDataSource);

		routingDataSource.setTargetDataSources(targetDataSources);

		return routingDataSource;
	}

	@Bean
	@Primary
	public DataSource datasource(@Qualifier("routingDataSource") DataSource routingDataSource) {
		return new LazyConnectionDataSourceProxy(routingDataSource);
	}

	@Bean
	public SqlSessionFactory sqlSessionFactory(DataSource dataSource) {
		final SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
		sessionFactory.setDataSource(dataSource);

		return sessionFactory.getObject();
	}

	@Bean
	public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sqlSessionFactory) {
		return new SqlSessionTemplate(sqlSessionFactory);
	}
}
```

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