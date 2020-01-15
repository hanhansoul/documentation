# Spring Security for REST

## 激活Spring Security

	@Config
	@EnableWebSecurity
	public class SecurityJavaConfig extends WebSecurityConfigurerAdapter {
	 
	    // ...
	}

## 配置Spring Security

	@Configuration
	@EnableWebSecurity
	public class SecurityJavaConfig extends WebSecurityConfigurerAdapter {
	 
	    @Override
		protected void configure(AuthenticationManagerBuilder auth) throws Exception {
		    auth.inMemoryAuthentication()
		        .withUser("admin").password(encoder().encode("adminPass")).roles("ADMIN")
		        .and()
		        .withUser("user").password(encoder().encode("userPass")).roles("USER");
		}
		 
		@Bean
		public PasswordEncoder  encoder() {
		    return new BCryptPasswordEncoder();
		}
	}

为API配置

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

### Entry Point

在REST服务访问一个没有授权的资源时，应当收到一个401状态码的返回响应。

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

### 

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