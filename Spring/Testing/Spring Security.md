- .apply(springSecurity) is required on MockMvc with Spring Security, which is why we can't autowire MockMvc
- @WithMockUser allows us to specify any username and it will work – does not have to be a registered user – it just sets the user in the security context.
	
```java
@WebMvcTest
public class BeerControllerIT {
 
    @Autowired
    WebApplicationContext wac;
 
    MockMvc mockMvc;
 
    //@MockBean
    //...
 
    @BeforeEach
    void setUp() {
        mockMvc = MockMvcBuilders
                .webAppContextSetup(wac)
                .apply(springSecurity())
                .build();
    }
 
    @WithMockUser("spring")
    @Test
    void findBeers() throws Exception{
        mockMvc.perform(get("/beers/find"))
                .andExpect(status().isOk())
                .andExpect(view().name("beers/findBeers"))
                .andExpect(model().attributeExists("beer"));
    }
 
    @Test
    void findBeersWithHttpBasic() throws Exception{
        mockMvc.perform(get("/beers/find").with(httpBasic("spring", "guru")))
                .andExpect(status().isOk())
                .andExpect(view().name("beers/findBeers"))
                .andExpect(model().attributeExists("beer"));
    }
 
}
```