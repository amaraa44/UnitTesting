## Unit testek mockitóval

---

```
    @InjectMocks
    private KafkaLoggerUtil kafkaLoggerUtil;
    @Mock
    private ObjectMapper objectMapper;
    @Mock
    private FieldValueExtractor extractor;
```
`@InjectMocks` - Az az osztály amit tesztelni akarsz.

`@Mock` - Az az osztály amit mockolni szeretnél. A mockolt osztályok a `@InjectMocks` osztály fieldjei.

---

#### Mock annotációk inicializálása.
```
    @BeforeEach
    void setUp() throws Exception {
        MockitoAnnotations.initMocks(this);
    }
```

---

#### Teszt logger
```
import org.apache.logging.log4j.Level;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.core.Appender;
import org.apache.logging.log4j.core.LogEvent;
import org.apache.logging.log4j.core.Logger;
import org.mockito.ArgumentCaptor;
import org.mockito.Captor;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.*;

public class TestLog4j2Logger {

    @Mock
    private Appender appender;
    @Captor
    private ArgumentCaptor<LogEvent> captorLoggingEvent;
    private Logger logger;

    public void setUp(Class<?> loggerUtilClass){
        MockitoAnnotations.initMocks(this);

        when(appender.getName()).thenReturn("MockAppender");
        when(appender.isStarted()).thenReturn(true);
        when(appender.isStopped()).thenReturn(false);

        logger = (Logger) LogManager.getLogger(loggerUtilClass);
        logger.addAppender(appender);
        logger.setLevel(Level.INFO);
    }

    public void tearDown(){
        logger.removeAppender(appender);
    }

    public void assertLog(String expectedMessage, Level errorLevel, Class<? extends Throwable> expected) {
        verify(appender).append(captorLoggingEvent.capture());
        assertEquals(errorLevel, captorLoggingEvent.getValue().getLevel());
        assertEquals(expected, captorLoggingEvent.getValue().getThrown().getClass());
        assertEquals(expectedMessage,
                captorLoggingEvent.getValue().getMessage().toString());
    }

    public void doesNotLog() {
        verify(appender, times(0)).append(captorLoggingEvent.capture());
    }

}
```

---

#### Verify
`verify(MOCK, NEM_KÖTELEZŐ).metodus(any());`

`verify(extractor, times(3)).getStringFromField(any(), anyString(), anyLong());`

---

#### When
###### thenThrow()
`when(objectMapper.writeValueAsString(any())).thenThrow(RuntimeException.class);`
###### thenReturn()
`when(objectMapper.writeValueAsString(any())).thenReturn(PAYLOAD_AS_JSON_MOCK_RESULT);`

---

#### Assertions
###### assertNull()
`assertNull(event.getErrorCode());`
###### assertEquals()
`assertEquals(STRING_FROM_FIELD_MOCK_RESULT, event.getProducer());`
###### assertTrue()
`assertTrue(event.isSucces());`

---

#### Spy
###### TODO
