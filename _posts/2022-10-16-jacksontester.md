---
layout: post
title: Generate JSON in Spring Boot tests
categories: spring
---

JacksonTester is a utility class included in the Spring Boot Test module to generate and parse JSON.

For example, to easily compare the response from a REST API with an object `expectedStatus` of type `JobStatusDto`.

```java
@WebMvcTest(TransactionController.class)
@AutoConfigureJsonTesters
public class TransactionControllerTest {

    @MockBean
    private TransactionService transactionService;

    // This object will be initialised thanks to @AutoConfigureJsonTesters
    @Autowired
    private JacksonTester<JobStatusDto> jsonJobStatus;

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void testGetJobStatus() throws Exception {
        // given
        long pJobId = 1L;
        JobStatusDto expectedStatus = new JobStatusDto(pJobId, "status", "message", "detailedMessage");
        given(transactionService.getJobStatus(pJobId)).willReturn(expectedStatus);

        // when
        MockHttpServletResponse response = mockMvc.perform(MockMvcRequestBuilders.get("/api/v1/job/1")).andReturn().getResponse();

        // then
        assertThat(response.getStatus()).isEqualTo(HttpStatus.OK.value());
        assertThat(response.getContentAsString())
                .isEqualTo(jsonJobStatus.write(expectedStatus).getJson());
    }
    
}
```

See also:

* [The Practical Developer - How to test a controller in Spring Boot.](https://thepracticaldeveloper.com/guide-spring-boot-controller-tests/#strategy-2-spring-mockmvc-example-with-webapplicationcontext)
* [TransactionControllerTest.java](https://github.com/joekeen/processed-future-movement/blob/main/src/test/java/au/id/keen/pfm/controller/TransactionControllerTest.java)