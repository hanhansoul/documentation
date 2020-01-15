# DTO

## Model Mapper

使用ModelMapper实现实体向DTO的转换。

	<dependency>
	    <groupId>org.modelmapper</groupId>
	    <artifactId>modelmapper</artifactId>
	    <version>2.3.2</version>
	</dependency>

	@Bean
	public ModelMapper modelMapper() {
	    return new ModelMapper();
	}

## DTO

	public class PostDto {
	    private static final SimpleDateFormat dateFormat
	      = new SimpleDateFormat("yyyy-MM-dd HH:mm");
	 
	    private Long id;
	 
	    private String title;
	 
	    private String url;
	 
	    private String date;
	 
	    private UserDto user;
	 
	    public Date getSubmissionDateConverted(String timezone) throws ParseException {
	        dateFormat.setTimeZone(TimeZone.getTimeZone(timezone));
	        return dateFormat.parse(this.date);
	    }
	 
	    public void setSubmissionDate(Date date, String timezone) {
	        dateFormat.setTimeZone(TimeZone.getTimeZone(timezone));
	        this.date = dateFormat.format(date);
	    }
	 
	    // standard getters and setters
	}

## Service层

	public List<Post> getPostsList(
	  int page, int size, String sortDir, String sort) {
	  
	    PageRequest pageReq
	     = PageRequest.of(page, size, Sort.Direction.fromString(sortDir), sort);
	  
	    Page<Post> posts = postRepository
	      .findByUser(userService.getCurrentUser(), pageReq);
	    return posts.getContent();
	}

## Controller层

	@Controller
	class PostRestController {
	 
	    @Autowired
	    private IPostService postService;
	 
	    @Autowired
	    private IUserService userService;
	 
	    @Autowired
	    private ModelMapper modelMapper;
	 
	    @RequestMapping(method = RequestMethod.GET)
	    @ResponseBody
	    public List<PostDto> getPosts(...) {
	        //...
	        List<Post> posts = postService.getPostsList(page, size, sortDir, sort);
	        return posts.stream()
	          .map(post -> convertToDto(post))
	          .collect(Collectors.toList());
	    }
	 
	    @RequestMapping(method = RequestMethod.POST)
	    @ResponseStatus(HttpStatus.CREATED)
	    @ResponseBody
	    public PostDto createPost(@RequestBody PostDto postDto) {
	        Post post = convertToEntity(postDto);
	        Post postCreated = postService.createPost(post));
	        return convertToDto(postCreated);
	    }
	 
	    @RequestMapping(value = "/{id}", method = RequestMethod.GET)
	    @ResponseBody
	    public PostDto getPost(@PathVariable("id") Long id) {
	        return convertToDto(postService.getPostById(id));
	    }
	 
	    @RequestMapping(value = "/{id}", method = RequestMethod.PUT)
	    @ResponseStatus(HttpStatus.OK)
	    public void updatePost(@RequestBody PostDto postDto) {
	        Post post = convertToEntity(postDto);
	        postService.updatePost(post);
	    }
	}

DTO转换器

	private PostDto convertToDto(Post post) {
	    PostDto postDto = modelMapper.map(post, PostDto.class);
	    postDto.setSubmissionDate(post.getSubmissionDate(), 
	        userService.getCurrentUser().getPreference().getTimezone());
	    return postDto;
	}

	private Post convertToEntity(PostDto postDto) throws ParseException {
	    Post post = modelMapper.map(postDto, Post.class);
	    post.setSubmissionDate(postDto.getSubmissionDateConverted(
	      userService.getCurrentUser().getPreference().getTimezone()));
	  
	    if (postDto.getId() != null) {
	        Post oldPost = postService.getPostById(postDto.getId());
	        post.setRedditID(oldPost.getRedditID());
	        post.setSent(oldPost.isSent());
	    }
	    return post;
	}