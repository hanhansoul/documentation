# REST应用的认证与授权

## 配置Spring Security

@EnableWebSecurity：Spring Seuciryt是完全基于Servlet Filter的，注册Servlet Filter最简单的方式是通过@EnableWebSecurity注解。

	@Configuration
	@EnableWebSecurity
	public class SecurityJavaConfig extends WebSecurityConfigurerAdapter {
	    // ...
	}

	@Override
	protected void configure(AuthenticationManagerBuilder auth) throws Exception {
	    auth.inMemoryAuthentication()
	        .withUser("admin")
	        .password(encoder().encode("adminPass"))
	        .roles("ADMIN")
	        .and()
	        .withUser("user")
	        .password(encoder().encode("userPass"))
	        .roles("USER");
	}
	 
	@Bean
	public PasswordEncoder  encoder() {
	    return new BCryptPasswordEncoder();
	}

配置API endpoint

	@Override
	protected void configure(HttpSecurity http) throws Exception { 
	    http
	    .csrf().disable()
	    .exceptionHandling()
	    .authenticationEntryPoint(restAuthenticationEntryPoint)
	    .and()
	    .authorizeRequests()
	    .antMatchers("/api/foos").authenticated()
	    .antMatchers("/api/admin/**").hasRole("ADMIN")
	    .and()
	    .formLogin()
	    .successHandler(mySuccessHandler)
	    .failureHandler(myFailureHandler)
	    .and()
	    .logout();
	}

### http元素

HttpSecurity类型的http对象用于配置认证与授权要求。

### 入口点

在标准的web应用中，一般在一个为认证用户访问被保护资源时会自动触发认证过程，该过程会将用户重定向到登录页面以输入认证信息。

对于REST应用，我们应该只能通过一个对特定URI的request请求进行认证，如果认证失败，则请求失败并返回401 UNAUTHORIZED状态码。

Spring Security通过Entry Point的概念实现该认证过程的自动触发。该Entry Point通过authenticationEntryPoint方法注入。

定义对所有请求都返回401的EntryPoint：

	@Component
	public final class RestAuthenticationEntryPoint 
	  implements AuthenticationEntryPoint {
	 
	    @Override
	    public void commence(
	        HttpServletRequest request, 
	        HttpServletResponse response, 
	        AuthenticationException authException) throws IOException {
	         
	        response.sendError(HttpServletResponse.SC_UNAUTHORIZED, 
	          "Unauthorized");
	    }
	}

### Login表单

Spring Security通过org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter实现登录表单

对于REST应用，认证成功应当返回200OK。我们可以通过在表单登录filter中注入自定义的认证成功处理器实现。

	public class MySavedRequestAwareAuthenticationSuccessHandler 
	  extends SimpleUrlAuthenticationSuccessHandler {
	 
	    private RequestCache requestCache = new HttpSessionRequestCache();
	 
	    @Override
	    public void onAuthenticationSuccess(
	      HttpServletRequest request,
	      HttpServletResponse response, 
	      Authentication authentication) 
	      throws ServletException, IOException {
	  
	        SavedRequest savedRequest
	          = requestCache.getRequest(request, response);
	 
	        if (savedRequest == null) {
	            clearAuthenticationAttributes(request);
	            return;
	        }
	        String targetUrlParam = getTargetUrlParameter();
	        if (isAlwaysUseDefaultTargetUrl()
	          || (targetUrlParam != null
	          && StringUtils.hasText(request.getParameter(targetUrlParam)))) {
	            requestCache.removeRequest(request, response);
	            clearAuthenticationAttributes(request);
	            return;
	        }
	 
	        clearAuthenticationAttributes(request);
	    }
	 
	    public void setRequestCache(RequestCache requestCache) {
	        this.requestCache = requestCache;
	    }
	}

认证失败时返回401。

