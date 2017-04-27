> Pageable

```
/**
 * <pre>
 * 分页对象.
 * param参数为要查询的参数信息,如:username="zhangsan", 
 * name在MyBatis里面就是<if test="param.zhangsan != null"> ...
 * orders为数据排序信息, 可以多个,并且有序.
 * 其中total在MyBatis拦截器拦截会自动设置其值.
 * </pre>
 * @author luoyh(Roy) - Apr 26, 2017
 */
public class Pageable<T> implements Serializable {
	private static final long serialVersionUID = 1L;
	public static final int DEFAULT_START = 1;
	public static final int DEFAULT_SIZE = 20;
	
	private int start = DEFAULT_START;	// 当前页,从1开始
	private int size = DEFAULT_SIZE;	// 每页大小
	private int pages;	// 总页数
	private int total;	// 总大小
	
	private Map<String, Object> param = new HashMap<>();	// 参数列表
	private LinkedHashMap<String, Order> orders = new LinkedHashMap<>();	// 排序
	
	private List<T> data;	// 数据
	
	public static <T> Pageable<T> me() {
		return new Pageable<T>();
	}
	
	public static enum Order {
		ASC, DESC;
	}
	
	public LinkedHashMap<String, Order> getOrders() {
		return orders;
	}
	public void setOrders(LinkedHashMap<String, Order> orders) {
		this.orders = orders;
	}
	public int getStart() {
		return start;
	}
	public void setStart(Integer start) {
		this.start = null == start ? DEFAULT_START : start;
	}
	public int getSize() {
		return size;
	}
	public void setSize(Integer size) {
		this.size = null == size ? DEFAULT_SIZE : size;
	}
	public int getTotal() {
		return total;
	}
	public void setTotal(int total) {
		this.total = total;
	}
	public Map<String, Object> getParam() {
		return param;
	}
	public void setParam(Map<String, Object> param) {
		this.param = param;
	}
	public int getPages() {
		pages = total / size + (total % size == 0 ? 0 : 1);
		return pages;
	}
	public void setPages(int pages) {
		this.pages = pages;
	}
	public List<T> getData() {
		return data;
	}
	public void setData(List<T> data) {
		this.data = data;
	}
	
}
```

> Oracle
```
/**
 * MyBatis page plugin.
 * @author luoyh(Roy) - Apr 26, 2017
 */
@Intercepts({
	@Signature(
			method = "prepare",
			type = StatementHandler.class,
			args = { Connection.class } // if +3.4, the arguments are two: Connection.class, Integer.class
)})
public class PageInterceptor implements Interceptor {
	
	private String seprator;
	private int sepratorLength = 0;

	@Override
	public Object intercept(Invocation invocation) throws Throwable {
		StatementHandler statementHandler = (StatementHandler) invocation.getTarget();
		BoundSql boundSql = statementHandler.getBoundSql();
		Object param = boundSql.getParameterObject();
		
		if (param instanceof Pageable) {
			MetaObject metaObject = SystemMetaObject.forObject(statementHandler);
			String origin = boundSql.getSql();
			int index = origin.indexOf(seprator);
			
			String prefix = origin.substring(0, index);
			StringBuilder suffix = new StringBuilder(origin.substring(index + sepratorLength));
			
			Pageable<?> page = (Pageable<?>) param;
			
			String total = "select count(1) from " + suffix.toString();
			Connection connection = (Connection) invocation.getArgs()[0]; // don't close
			try (PreparedStatement countStatement = connection.prepareStatement(total)) {
				ParameterHandler parameterHandler = (ParameterHandler)metaObject.getValue("delegate.parameterHandler");
				parameterHandler.setParameters(countStatement);
				try (ResultSet rs = countStatement.executeQuery()) {
					if(rs.next()) {
						page.setTotal(rs.getInt(1));
					}
				}
			}
			
			// append order by.
			if (!page.getOrders().isEmpty()) {
				suffix.append(" order by ");
				int idx = 0;
				for (Map.Entry<String, Order> entry : page.getOrders().entrySet()) {
					if (idx ++ > 0) {
						suffix.append(" , ");
					}
					suffix.append(entry.getKey()).append(" ").append(entry.getValue().name());
				}
			}
			
                        // if MySQL, use limit or offset, it's easy.
			StringBuilder sql = new StringBuilder();
			sql.append("select * from (select ROWNUM rw, a.* from ( ").append(prefix).append(" from ")
			.append(suffix).append(") a where rownum < ").append(page.getStart() * page.getSize() + 1)
			.append(") b where b.rw > ").append((page.getStart() - 1) * page.getSize());
			
			metaObject.setValue("delegate.boundSql.sql", sql.toString());
		}
		return invocation.proceed();
	}

	@Override
	public Object plugin(Object target) {
		return Plugin.wrap(target, this);
	}

	@Override
	public void setProperties(Properties properties) {
		// like %from% #FRoM# $foRM$ _form_ , default FROM
		// first FROM
		this.seprator = properties.getProperty("seprator", "FROM");
		this.sepratorLength = this.seprator.length();
	}

}

```