---
title: 스프링 기반 REST API 개발04 - 이벤트 조회 및 수정 REST API 개발(PagedResouce)
date: 2019-08-09 11:29:44
tags: RestAPI
toc: true
widgets:
  - type: toc
    position: right
  - type: tagcloud
    position: right
sidebar:
  right:
    sticky: true
---
# 스프링 기반 REST API 개발(inflearn)4 - 백기선

## 이벤트 조회 및 수정 REST API 개발
<!-- more -->
### 이벤트 목록 조회 API 구현

#### 페이징, 정렬
- 스프링 데이터 JPA가 제공하는 pageable을 사용한다.
- GET방식 parameter
  - page : 원하는 페이지 (0부터 시작)
  - size : 한페이지에 들어갈 리소스 수 (기본값 = 20)
  - sort : 정렬타입 및 차순 (ex.. sort=name,DESC)
- Return 
  - pageable : pageable 정보
  - content : 리소스 정보
  - totalElements : 전체 리소스 수
  - …
  
    ```
    @GetMapping("/api/events")
    public ResponseEntity queryEvents(Pageable pageable) {
        Page<Event> page = eventsRepository.findall(pagealbe);
      return ResponseEntity.ok(page);
    }
    ```

#### PagedResouce

- PagedResourceAssembler<T>를 사용하여 Page객체를 PagedResouce를 생성할 수 있다.
  - 첫페이지, 이전페이지, 현재페이지, 다음페이지, 마지막페이지 Link가 포함되어있다.
  - Resource<Event>로 사용할 경우, 각 리소스들의 링크정보는 포함되지 않는다.
    - 이전에 생성했던 EventResource로 적용시킨다. ( e -> new EventResouce(e); )
    ![restapi04-1](/images/restapi/restapi04-1.png)

- Profile 링크 추가
    ```
    @GetMapping("/api/events")
    public ResponseEntity queryEvents(Pageable pageable, PagedResourcesAssembler<Event> assembler) {
        Page<Event> page = eventRepository.findAll(pageable);
        PagedResources<Resource<Event>> pagedResources = assembler.toResource(page, e -> new EventResource(e));
        pagedResources.add(new Link("/docs/index.html#resources-events-list").withRel("profile"));
        return ResponseEntity.ok(pagedResources);
    }
    ```

- 테스트 (문서화는 생략)
  ```
  @Test
  @TestDescription("30개 이벤트를 10개씩 두번째 페이지 조회하기")
  public void queryEvents() throws Exception {
      // given
      IntStream.range(0, 30).forEach(this::generateEvent); // mehtod reference로 간결에가 사용이 가능

      // when
      this.mockMvc.perform(get("/api/events")
                  .param("page", "1")
                  .param("size", "10")
                  .param("sort", "name,DESC")
              )
              .andDo(print())
              .andExpect(status().isOk())
              .andExpect(jsonPath("page").exists())
              .andExpect(jsonPath("_embedded.eventList[0]._links.self").exists())
              .andExpect(jsonPath("_links.self").exists())
              .andExpect(jsonPath("_links.profile").exists())
              .andDo(document("query-events")) // 자세한 문서화 생략
      ;

  }

  private void generateEvent(int i) {
      Event event = Event.builder()
              .name("event" + i)
              .description("test eveent")
              .build();

      this.eventRepository.save(event);
  }
  ```
<br><br>

### 이벤트 조회 API 구현

#### 단일 조회 API

```
@GetMapping("/{id}")
public ResponseEntity getEvent(@PathVariable Integer id) {
  Optional<Event> byId = eventRepository.findById(id);
  if (byId.isPresent()) {
    Event event = byId.get();
    EventResource resource = new EventResource(event);
    resource.add(new Link("/docs/index.html#resources-events-get").withRel("profile"));
    return ResponseEntity.ok(resource);
  }

  return ResponseEntity.notFound().build();
}
```

- 해당아이디에 해당하는 이벤트를 찾아서 조회한다.
  - EventResource를 ResponseEntity body에 담아서 리턴(self 링크를 포함하고 있다.)
  - profile 링크를 추가
- isPrsent() : 해당 이벤트가 있으면 true 아니면 false를 반환
- ResponseEntity.notFound().build(); -> 404에러를 반환

#### 테스트

- 정상조회
  ```
  @Test
  @TestDescription("기존의 이벤트 하나 조회하기")
  public void getEvent() throws Exception {
    //given
    Event event = this.generateEvent(100);
  
    //when
    ResultActions perform = this.mockMvc.perform(get("/api/events/{id}", event.getId()))
      .andExpect(status().isOk())
      .andExpect(jsonPath("name").exists())
      .andExpect(jsonPath("id").exists())
      .andExpect(jsonPath("_links.self").exists())
      .andExpect(jsonPath("_links.profile").exists());  
  
    //then
    perform.andDo(document("get-an-events"));
  }
  ```

- 없는 이벤트 조회시 404 응답
  ```
  @Test
  @TestDescription("없는 이벤트를 조회하면 404 응답받기")
  public void getEvent404() throws Exception {
    this.mockMvc.perform(get("/api/events/1142323"))
      .andExpect(status().isNotFound());
  }
  ```
<br><br>
### 이벤트 수정 API 구현

#### 이벤트 수정 API

```
@PutMapping("/{id}")
public ResponseEntity updateEvent(@PathVariable Integer id, 
                                  @RequestBody @Valid EventDto eventDto, Errors errors) {
  
  Optional<Event> byId = this.eventRepository.findById(id);

  if (!byId.isPresent()){
    return ResponseEntity.notFound().build();
  }

  if (errors.hasErrors()) {
    return badRequest(errors);
  }

  this.eventValidator.validate(eventDto, errors);
  if (errors.hasErrors()) {
    return badRequest(errors);
  }


  Event event = byId.get();
  modelMapper.map(eventDto, event);
  Event updateEvent = eventRepository.save(event);
//  eventRepository.flush();

  EventResource resource = new EventResource(updateEvent);
  resource.add(new Link("/docs/index.html#resources-events-update").withRel("profile"));

  return ResponseEntity.ok(resource);
}
```



#### 테스트코드
- 이벤트 정상 수정
  ```
  @Test
  @TestDescription("이벤트를 정상적으로 수정하기")
  public void updateEvent() throws Exception {
    // given
    Event event = this.generateEvent(200);
    EventDto eventDto = this.modelMapper.map(event, EventDto.class);
    eventDto.setName("수정한 이벤트");
  
  
    // when
    this.mockMvc.perform(put("/api/events/{id}", event.getId())
                         .contentType(MediaType.APPLICATION_JSON_UTF8)
                         .content(this.objectMapper.writeValueAsString(eventDto))
                         .accept(MediaTypes.HAL_JSON_UTF8))
                .andDo(print())
                .andExpect(status().isOk())
                .andExpect(jsonPath("name").value(eventDto.getName()))
                .andExpect(jsonPath("_links.self").exists())
                .andDo(document("update-event"));
  
    Event updateEvent = eventRepository.findById(event.getId()).get();
    assertThat(updateEvent.getName()).isEqualTo(eventDto.getName());
  }
  ```

- 수정하려는 이벤트가 없는경우 404 Not Found
  ```
  @Test
  @TestDescription("존재하지 않는 이벤트 수정 404 응답")
  public void updateEvent404_NotFound() throws Exception {
    // given
    Event event = this.generateEvent(200);
    EventDto eventDto = this.modelMapper.map(event, EventDto.class);
  
    // when
    this.mockMvc.perform(put("/api/events/123331")
                         .contentType(MediaType.APPLICATION_JSON_UTF8)
                         .content(this.objectMapper.writeValueAsString(eventDto))
                         .accept(MediaTypes.HAL_JSON_UTF8))
      .andDo(print())
      .andExpect(status().isNotFound());
  }
  ```

- 입력 데이터가 이상한 경우 400 Bad Request
  ```
  @Test
  @TestDescription("이벤트 입력값이 잘못된 경우 BadRequest 응답 받기")
  public void updateEvent400_Empty() throws Exception {
    // given
    Event event = this.generateEvent(200);
  
    // when
    this.mockMvc.perform(put("/api/events/{id}", event.getId())
                         .contentType(MediaType.APPLICATION_JSON_UTF8)
                         .accept(MediaTypes.HAL_JSON_UTF8))
      .andDo(print())
      .andExpect(status().isBadRequest());
  }
  ```

- 도메인로직으로 데이터 검증 실패 400 Bad Request
    ```
    @Test
    @TestDescription("이벤트 입력값이 잘못된 경우 BadRequest 응답 받기")
    public void updateEvent400_Wrong() throws Exception {
    //  given
    Event event = this.generateEvent(200);
    EventDto eventDto = this.modelMapper.map(event, EventDto.class);
    eventDto.setBasePrice(20000);
    eventDto.setMaxPrice(1000);  
    
    //  when
    this.mockMvc.perform(put("/api/events/{id}", event.getId())
                         .contentType(MediaType.APPLICATION_JSON_UTF8)
                         .content(this.objectMapper.writeValueAsString(eventDto))
                         .accept(MediaTypes.HAL_JSON_UTF8))
      .andDo(print())
      .andExpect(status().isBadRequest());
    }
    ```



### 테스트 코드 리팩토링

- 중복 코드 제거 하기
- 중복되는 코드들을 뽑아 테스트 클래스를 작성 후 상속한다.
  - 상속 클래스에는 @Ignore 어노테이션으로 테스트로 간주되지 않도록 설정한다.
    ```
    @RunWith(SpringRunner.class)
    @SpringBootTest
    @AutoConfigureMockMvc
    @AutoConfigureRestDocs
    @Import(RestDocsConfiguration.class)
    @ActiveProfiles("test")
    @Ignore
    public class BaseControllerTest {
    
        @Autowired
        protected MockMvc mockMvc;
    
        @Autowired
        protected ModelMapper modelMapper;
    
        @Autowired
        protected ObjectMapper objectMapper;
    }
    ```

- 상속받는 테스트들에서 상속. (중복된 내용 제거)
  ```
  public class EventControllerTest extends BaseControllerTest {
    ...
  }
  ```

