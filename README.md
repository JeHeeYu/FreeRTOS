# FreeRTOS
FreeRTOS API Reference 정리 Repository

## Variable Naming Rules
FreeRTOS는 변수명을 결정할 때 접두어를 사용한다.
<ul>
<li><b>c</b> : char 타입과 동일</li>
<li><b>s</b> : int16_t 타입으로 short 타입과 동일</li>
<li><b>i</b> : int32_t 타입으로 int 타입과 동일</li>
<li><b>x</b> : BaseType_t 타입을 의미하며 구조체, 인스턴스 핸들 등으로 사용되며 대부분 x타입을 사용</li>
<li><b>u</b> : unsigned 타입과 동일</li>
<li><b>p</b> : 포인터 변수를 의미</li>
</ul>

## Function Naming Rules
FreeRTOS는 함수명을 결정할 때 접두어를 사용한다.
<ul>
<li><b>v</b> : void 타입으로 함수 반환 값이 없음</li>
<li><b>x</b> : BaseType_t 타입을 의미하며 구조체, 인스턴스 핸들 등으로 사용되며 대부분 x타입을 사용</li>
<li><b>pv</b> : void* 타입을 반환하는 함수
<li><b>prv</b> : private 함수를 의미. 대표적으로 task가 실행되지 않을 때 실행되는 idle 태스크가 호출하는 callback 함수(hook)이 이 접두어를 사용</li>
</ul>

## Macro Prefix Naming Rules
FreeRTOS는 매크로에서 접두어가 사용되며, 대부분 댐누자로 쓰여지며 정의 된 위치를 나타낼 때는 소문자로 시작된다.
<ul>
<li><b>port</b> : portable.h, portmacro.h ... (Ex. portMAXDELAY)</li>
<li><b>task</b> : task.h (Ex. taskENTERCRITICAL())</li>
<li><b>pd</b> : projdefs.h (Ex. pdTRUE)</li>
<li><b>config</b> : FreeRTOSConfig.h (Ex. configUSERPREEMPTION)</li>
<li><b>err</b> : projdefs.h (Ex. errQUEUEFULL)</li>
</ul>

## Return Value
다음 값은 함수 반환 값을 나타낸다.
<ul>
<li><b>pdTRUE</b> : 1</li>
<li><b>pdFALSE</b> : 0</li>
<li><b>pdFASS</b> : 1</li>
<li><b>pdFAIL</b> : 0</li>
</ul>

## xTaskCreate
BaseType_t xTaskCreate(TaskFunction_t pvTaskCode, const char* const pcName, 
<br>
　　　　　　　　　　　configSTACK_DEPTH_TYPE usStackDepth, void *pvParameters, UBaseType_t uxPriority, TaskHandle_t *pxCreatedTask);
<br>
<b>Description</b> : 태스크를 중지(Suspend) 시키는 API. 중지된 태스크는 우선순위에 관계없이 CPU 처리 시간을 할당받지 못함.
<br>
　　　　　　xTaskSuspend를 같은 Task에 대해 두 번 호출하더라도 중지된 Task를 다시 준비 상태로 돌리기 위해서는 
<br>
　　　　　　xTaskResume() 함수를 한 번 호출하면 됨
<br>
<b>Header</b> : task.h
<br>
<b>Parameter</b>
<br>
　　pvTaskCode : Task 함수
<br>
　　pcName : Task 함수 이름으로 String 형태로 전달
<br>
　　usStackDepth : 스택 크기로 Word 타입
<br>
　　pvParameters : 태스크 생성 시 전달할 매개변수 
<br>
　　uxPriority : 태스크 우선순위로 높을 수록 우선순위가 높음
<br>
　　pxCreatedTask : 태스크 핸들러로 태스크 TaskHandle_t 타입 또는 NULL 전달
<br>
<b>Return</b>
<br>
　　pdPASS : 태스크 정상적으로 생성 시 반환
<br>
　　errCOULD_NOT_ALLOCATE_REQUIRED_MEMORY : 태스크 생성 실패
<br>
<b>Example</b>
<pre>
// 태스크 생성 시 실행되는 함수
void vTaskCode( void * pvParameters )
{
    // 태스크 생성 시 1을 전달하였으므로 1 전달 받음
    configASSERT( ( ( uint32_t ) pvParameters ) == 1 );

    for( ;; )
    {
        // 작업할 코드
    }
}

// 태스크 생성 함수
void vOtherFunction( void )
{
BaseType_t xReturned;
TaskHandle_t xHandle = NULL;

    // 태스크 핸들러 생성
    xReturned = xTaskCreate(
                    vTaskCode,          // 태스크 생성 구현 함수
                    "NAME",             // 태스크 생성 이름
                    STACK_SIZE,         // 스택 크기
                    ( void * ) 1,       // 태스크 생성 시 전달할 매개변수
                    tskIDLE_PRIORITY,   // 태스크 생성 우선 순위
                    &xHandle );         // 태스크 생성 핸들러

    if( xReturned == pdPASS )
    {
        // 태스크 정상 생성 후 삭제 함수
        vTaskDelete( xHandle );
    }
}
</pre>




## vTaskSuspend
void vTaskSuspend(TaskHandle_t xTaskToSuspend);
<br>
<b>Description</b> : 태스크를 중지(Suspend) 시키는 API. 중지된 태스크는 우선순위에 관계없이 CPU 처리 시간을 할당받지 못함.
<br>
　　　　　　xTaskSuspend를 같은 Task에 대해 두 번 호출하더라도 중지된 Task를 다시 준비 상태로 돌리기 위해서는 
<br>
　　　　　　xTaskResume() 함수를 한 번 호출하면 됨
<br>
<b>Header</b> : task.h
<br>
<b>Parameter</b>
<br>
　　xTaskToSuspend : 중지시킬 태스크의 핸들러로 NULL을 넘겨주면 호출한 태스크가 중지됨
<br>
<b>Return</b>
<br>
　　void
<br>
<b>Example</b>
<pre>
void vAFunction (void)
{
    // 태스트 생성
    xTaskCreate(vTaskCode, "NAME", STACK_SIZE, NULL, taskIDLE_PRIORITY, &xHandle);
    
    // 중지시킬 태스크
    vTaskSuspend(xHandle);
    vTaskSuspend(NULL);
}
</pre>


## vTaskDelay
void vTaskDelay(const TickType_t xTicksToDelay);
<br>
<b>Description</b> : 매개변수의 숫자만큼의 틱 동안 태스크를 지연 시킴. 보통 상수 portTICK_PERIOD_MS를 이용하여 1틱 주기의 분해능으로 
<br>
　　　　　　실시간 속도를 계산함
<br>
<b>Header</b> : task.h
<br>
<b>Parameter</b>
<br>
　　xTicksToDelay : 지연 시킬 시간
<br>
<b>Return</b>
<br>
　　void
<br>
<b>Example</b>
<pre>
 void vTaskFunction( void * pvParameters )
 {
 // 500ms 지연
 const TickType_t xDelay = 500 / portTICK_PERIOD_MS;

     for( ;; )
     {
         // 500ms 마다 LED Toggle
         vToggleLED();
         vTaskDelay( xDelay );
     }
}

</pre>


## vTaskPrioritySet
void vTaskPrioritySet(TaskHandle_t xTask, UBaseType_t uxNewPriority);
<br>
<b>Description</b> : 태스크의 우선순위를 지정하는 함수로 현재 실행되는 태스크의 우선순위보다 더 높은 우선순위를 
<br>
　　　　　　지정하면 함수가 반환되기 전에 문맥 전환이 발생한다.
<br>
<b>Header</b> : task.h
<br>
<b>Parameter</b>
<br>
　　xTask : 우선순위가 수정될 태스크의 핸들러. NULL 설정 시 현재 태스크의 우선순위 변경
<br>
　　uxNewPriority : 태스크가 새롭게 가질 우선순위
<br>
<b>Return</b>
<br>
　　void
<br>
<b>Example</b>
<pre>
 void vAFunction( void )
 {
 TaskHandle_t xHandle;
     // 태스크 생성
     xTaskCreate( vTaskCode, "NAME", STACK_SIZE, NULL, tskIDLE_PRIORITY, &xHandle );
     
     // 태스크 우선순위 설정
     vTaskPrioritySet( xHandle, tskIDLE_PRIORITY + 1 )
     
     // NULL 설정 시 현재 태스크 우선 순위 설정
     vTaskPrioritySet( NULL, tskIDLE_PRIORITY + 1 );
 }
</pre>


## vTaskDelete
void vTaskDelete(TaskHandle_t xTask);
<br>
<b>Description</b> : 현재 동작 중인 태스크를 제거한다. 제거 후 다음 우선순위 태스크가 동작한다.
<br>
<b>Header</b> : task.h
<br>
<b>Parameter</b>
<br>
　　xTask : 제거할 태스크의 핸들러로 NULL 전달 시 함수를 호출한 태스크 자체가 제거됨
<br>
<b>Return</b>
<br>
　　void
<br>
<b>Example</b>
<pre>
 void vOtherFunction( void )
 {
 TaskHandle_t xHandle = NULL;

     // 새로운 태스크 생성
     xTaskCreate( vTaskCode, "NAME", STACK_SIZE, NULL, tskIDLE_PRIORITY, &xHandle );

     // 태스크가 동작 중일 경우 제거
     if( xHandle != NULL )
     {
         vTaskDelete( xHandle );
     }
 }
</pre>


## vTaskResume
void vTaskResume(TaskHandle_t xTaskToResume);
<br>
<b>Description</b> : 중지(suspend)된 태스크를 재개(resume)시키는 함수로 이전에 vTaskSuspend()에 중지된 함수를 재시작 함
<br>
<b>Header</b> : task.h
<br>
<b>Parameter</b>
<br>
　　xTask : 재시작할 태스크 핸들
<br>
<b>Return</b>
<br>
　　void
<br>
<b>Example</b>
<pre>
 void vAFunction( void )
 {
 TaskHandle_t xHandle;

     // 태스크 생성
     xTaskCreate( vTaskCode, "NAME", STACK_SIZE, NULL, tskIDLE_PRIORITY, &xHandle );

     // 태스크 중지
     vTaskSuspend( xHandle );

     // 일시 중단된 작업 시작
     vTaskResume( xHandle );

     // The created task will once again get microcontroller processing
     // time in accordance with its priority within the system.
 }
   
</pre>





## vSemaphoreCreateBinary
void vSemaphoreCreateBinary(SemaphoreHandle_t xSemaphore);
<br>
<b>Description</b> : 바이너리 세마포어를 생성하는 함수 바이너리 세마포어 이므로 0과 1의 값만 전달할 수 있음
<br>
<b>Header</b> : semphr.h
<br>
<b>Parameter</b>
<br>
　　xSemaphore : 생성된 세마포어의 핸들
<br>
<b>Return</b>
<br>
　　void
<br>
<b>Example</b>
<pre>
SemaphoreHandle_t xSemaphore;

void vATask( void * pvParameters )
{
    // 
    vSemaphoreCreateBinary( xSemaphore );

    if( xSemaphore != NULL )
    {
        // 세마포어 생성 완료
    }
}
   
</pre>





## xSemaphoreCreateBinary
SemaphoreHandle_t xSemaphoreCreateBinary(void);
<br>
<b>Description</b> : 이진 바이너리 세마포어를 생성하는 함수로 빈 세마포어가 생성됨
<br>
<b>Header</b> : semphr.h
<br>
<b>Parameter</b>
<br>
　　void
<br>
<b>Return</b>
<br>
　　void
<br>
<b>Example</b>
<pre>
SemaphoreHandle_t xSemaphore;

void vATask( void * pvParameters )
{
    세마포어 생성
    xSemaphore = xSemaphoreCreateBinary();

    // 세마포어 생성 메모리(Heap)가 부족
    if( xSemaphore == NULL )
    {
        // 세마포어 생성 실패
    }
    else
    {
        세마포어 생성 완료
    }
}
</pre>





## vSemaphoreDelete
void vSemaphoreDelete(SemaphoreHandle_t xSemaphore);
<br>
<b>Description</b> : 세마포어 삭제. 세마포어 및 뮤텍스 재귀 세마포어도 삭제 가능 (삭제로 얻을 이득이 크지 않으므로 가급적 사용하지 않음)
<br>
<b>Header</b> : semphr.h
<br>
<b>Parameter</b>
<br>
　　xSemaphore : 삭제할 세마포어의 핸들
<br>
<b>Return</b>
<br>
　　void
<br>
<b>Example</b>
<pre>
SemaphoreHandle_t xSemaphore;

void vATask( void * pvParameters )
{
    // 
    vSemaphoreCreateBinary( xSemaphore );

    if( xSemaphore != NULL )
    {
        // 세마포어 생성 완료
    }
    
    vSemaphoreDelete(xSemaphore);
}
   
</pre>





## xSemaphoreTake
void xSemaphoreTake(SemaphoreHandle_t xSemaphore, TickType_t xTicksToWait);
<br>
<b>Description</b> : 세마포어를 얻기 위한 함수(대기)로 xSemaphoreCreateBinary() 또는 다른 생성 함수로 생성된 핸들러에 적용
<br>
<b>Header</b> : semphr.h
<br>
<b>Parameter</b>
<br>
　　xSemaphore : 사용 중인 세마포어 핸들
<br>
　　xTicksToWait : 세마포어를 얻을 때까지 기다리는 시간
<br>
<b>Return</b>
<br>
　　void
<br>
<b>Example</b>
<pre>
SemaphoreHandle_t xSemaphore = NULL;

// 세마포어 생성 함수
void vATask( void * pvParameters )
{
    // 뮤텍스 세마포어 생성
    xSemaphore = xSemaphoreCreateMutex();
}

// 세마포어를 사용하는 함수
void vAnotherTask( void * pvParameters )
{
    ... 

    if( xSemaphore != NULL )
    {
        // 세마포어를 얻을 때까지 대기하며 10Tick을 기다리면 무효
        if( xSemaphoreTake( xSemaphore, ( TickType_t ) 10 ) == pdTRUE )
        {
            // 세마포어 사용 가능

            // 세마포어 반환
            xSemaphoreGive( xSemaphore );
        }
        else
        {
            // 세마포어 생성 실패
        }
    }
}
</pre>





## xSemaphoreGive
void xSemaphoreGive(SemaphoreHandle_t xSemaphore);
<br>
<b>Description</b> : 세마포어 해제(반환) 하는 함수로 xSemaphoreCreateBinary() 또는 다른 생성 함수로 생성된 핸들러에 적용
<br>
<b>Header</b> : semphr.h
<br>
<b>Parameter</b>
<br>
　　xSemaphore : 해제할 세마포어 핸들
<br>
<b>Return</b>
<br>
　　void
<br>
<b>Example</b>
<pre>
 SemaphoreHandle_t xSemaphore = NULL;
 void vATask( void * pvParameters )
 {
    // 세마포어 생성
    xSemaphore = xSemaphoreCreateMutex();

    // 세마포어 생성 성공
    if( xSemaphore != NULL )
    {
        // 세마포어 반환할 수 없는 상태
        if( xSemaphoreGive( xSemaphore ) != pdTRUE )
        {
        }
        
        // 세마포어 사용 중인 상태 (즉, 반환 가능한 상태)
        if( xSemaphoreTake( xSemaphore, ( TickType_t ) 0 ) )
        {
            // 세마포어 사용 중
            
            // 세마포어 해제 시도
            if( xSemaphoreGive( xSemaphore ) != pdTRUE )
            {
                // 세마포어 해제 완료
            }
        }
    }
 }
</pre>






## xSemaphoreTakeFromISR
BaseType_t xSemaphoreTakeFromISR(SemaphoreHandle_t xSemaphore, signed BaseType_t *pxHigherPriorityTaskWoken);
<br>
<b>Description</b> : ISR에서 호출할 수 있는 xSemaphoreTake()의 버전
<br>
<b>Header</b> : semphr.h
<br>
<b>Parameter</b>
<br>
　　xSemaphore : Take할 세마포어 핸들
<br>
　　pxHigherPriorityTaskWoken : 휴먼상태에서 깨어날 때 결정할 문맥전환
<br>
<b>Return</b>
<br>
　　pdTRUE : 세마포어 Take 성공
<br>
　　pdFALSE : 세마포어 Take 실패
<br>






## xSemaphoreGiveFromISR
BaseType_t xSemaphoreGiveFromISR(SemaphoreHandle_t xSemaphore, signed BaseType_t *pxHigherPriorityTaskWoken);
<br>
<b>Description</b> : ISR에서 호출할 수 있는 xSemaphoreGive()의 버전
<br>
<b>Header</b> : semphr.h
<br>
<b>Parameter</b>
<br>
　　xSemaphore : Give할 세마포어 핸들
<br>
　　pxHigherPriorityTaskWoken : 휴먼상태에서 깨어날 때 결정할 문맥전환
<br>
<b>Return</b>
<br>
　　pdTRUE : 세마포어 Give 성공
<br>
　　pdFALSE : 세마포어 Give 실패
<br>






## xSemaphoreCreateCounting
SemaphoreHandle_t xSemaphoreCreateCounting(UBaseType_t uxMaxCount, UBaseType_t uxInitialCount);
<br>
<b>Description</b> : 카운팅 세마포어를 생성하고 참조할 수 있는 핸들 반환
<br>
<b>Header</b> : semphr.h
<br>
<b>Parameter</b>
<br>
　　uxMaxCount : 도달할 수 있는 최대 개수 값으로 세마포어가 이 값에 도달하면 더이상 주어진 수는 없음
<br>
　　uxInitialCount : 세마포어가 생성될 때 할당된 카운트 값
<br>
<b>Return</b>
<br>
　　SemaphoreHandle_t Handler : 세마포어에 대한 핸들
<br>
　　NULL : 세마포어를 유지하는데 필요한 RAM 부족 또는 만들 수 없을 때
<br>
<b>Example</b>
<pre>
 void vATask( void * pvParameters )
{
SemaphoreHandle_t xSemaphore;

    // 최대 개수가 10개이고 초기 개수가 0인 카운팅 세마포어 생성
    xSemaphore = xSemaphoreCreateCounting( 10, 0 );

    if( xSemaphore != NULL )
    {
        // 카운팅 세마포어 생성 성공
    }
}
</pre>






## uxSemaphoreGetCount
UBaseType_t xSemaphoreCreateCounting(SemaphoreHandle_t xSemaphore);
<br>
<b>Description</b> : 세마포어의 개수 반환
<br>
<b>Header</b> : semphr.h
<br>
<b>Parameter</b>
<br>
　　xSemaphore : 세마포어 
<br>
<b>Return</b>
<br>
　　Count : 세마포어가 카운팅 세마포어면서, 현재 카운트 값 반환
<br>
　　1 : 세마포어가 바이너리 세마포어면서, 사용할 수 있는 경우
<br>
　　0 : 세마포어를 사용할 수 없는 경우
<br>






## xSemaphoreCreateMutex
SemaphoreHandle_t xSemaphoreCreateMutex(void);
<br>
<b>Description</b> : 뮤텍스를 생성 및 뮤텍스를 참조할 수 있는 핸들 반환. 뮤텍스는 ISR
<br>
<b>Header</b> : semphr.h
<br>
<b>Parameter</b>
<br>
　　void
<br>
<b>Return</b>
<br>
　　SemaphoreHandle_t Handler : 뮤텍스 생성 성공 시 뮤텍스 핸들 반환
<br>
　　NULL : 뮤텍스를 생성하는데 필요한 메모리가 부족하거나 뮤텍스 생성 실패 시 반환
<br>
<b>Example</b>
<pre>
SemaphoreHandle_t xSemaphore;

void vATask( void * pvParameters )
{
   // 뮤텍스 생성
   xSemaphore = xSemaphoreCreateMutex();

   if( xSemaphore != NULL )
   {
       // 뮤텍스 생성 성공
   }
}
</pre>





## xEventGroupCreate
EventGroupHandle_t xEventGroupCreate(void);
<br>
<b>Description</b> : 새로운 RTOS 이벤트 그룹을 만들고 새로 만든 이벤트 그룹을 참조할 수 있는 핸들을 반환한다.
<br>
　　　　　　이벤트 그룹은 EventBits_t 유형의 변수에 저장된다.
<br>
　　　　　　이벤트 그룹 내에서 구현된 비트 수는 configUSE_16_BIT_TICKS가 1로 설정된 경우는 8, 0으로 설정되면 24이다.
<br>
<b>Header</b> : event_groups.h
<br>
<b>Parameter</b>
<br>
　　void
<br>
<b>Return</b>
<br>
　　EventGroupHandle_t Handle : 이벤트 그룹이 생성된 경우 이벤트 그룹 핸들 반환
<br>
　　NULL : 이벤트 그룹을 생성하는데 필요한 메모리가(Heap) 부족하거나 이벤트 그룹 생성 실패 시 반환
<br>
<b>Example</b>
<pre>
// 이벤트 그룹을 담을 변수 선언
EventGroupHandle_t xCreatedEventGroup;

// 이벤트 그룹 생성 시도
xCreatedEventGroup = xEventGroupCreate();

if( xCreatedEventGroup == NULL )
{
    // 사용 가능 FreeRTOS Heal 메모리가 부족하여 이벤트 그룹 생성 실패
}
else
{
    // 이벤트 그룹 생성 성공
}
</pre>





## xEventGroupWaitBits
EventBits_t xEventGroupWaitBits(const EventGroupHandle_t xEventGroup, const EventBits_t uxBitsToWaitFor,
　　　　　　　　　　　　　　　const BaseType_t xClearOnExit, const BaseType_t xWaitForAllBits, TickType_t xTicksToWait);
<br>
<b>Description</b> : RTOS 이벤트 그룹 내의 비트를 읽고 비트 또는 비트 그룹이 설정될 때까지 대기
<br>
<b>Header</b> : event_groups.h
<br>
<b>Parameter</b>
<br>
　　xEventGroup : xEventGroupCreate() 함수로 호출한 이벤트 그룹 핸들
  <br>
　　uxBitsToWaitFor : 테스트할 비트. define 값이나 enum 값, case 문 등 사용
  <br>
　　xClearOnExit : Clear 여부. pdTRUE or 1로 설정 시 시간 초과 이외 다른 이유로 반환되는 모든 비트 Clear (pdFALSE or 0 시 Clear 안함)
  <br>
　　xWaitForAllBits : 이벤트 대기 조건 방법. pdFALSE or 0 시 AND, pdTRUE or 1 시 OR
  <br>
　　xTicksToWait : uxBitsToWaitFor 매개변수 조건의 Timeout 시간
<br>
<b>Return</b>
<br>
　　EventBits_t bit : 대기 중인 이벤트 비트의 설정 값.
<br>
　　xEventGroupWaitBits() : 대기 중인 비트가 설정되지 않았을 때
<br>
<b>Example</b>
<pre>
#define BIT_0	( 1 << 0 )
#define BIT_4	( 1 << 4 )

void aFunction( EventGroupHandle_t xEventGroup )
{
    EventBits_t uxBits;
    
    // Timeout 시간 설정
    const TickType_t xTicksToWait = 100 / portTICK_PERIOD_MS;
    
    // 이벤트 그룹 내에서 BIT_0 또는 BIT_4가 설정될 때까지 최대 100ms 기다림 종료 전에 Clear 필요
    uxBits = xEventGroupWaitBits(
            xEventGroup,   // 테스트 중인 이벤트 그룹
            BIT_0 | BIT_4, // 대기할 이벤트 그룹 내의 비트로 BIT_0 또는 BIT_4
            pdTRUE,        // BIT_0 또는 BIT_4는 반환하기 전에 지워야 함(Clear)
            pdFALSE,       // 두 비트 모두 기다리지 않음
            xTicksToWait ); // 두 비트 중 하나가 설정될 때까지 최대 100ms 기다림

    if( ( uxBits & ( BIT_0 | BIT_4 ) ) == ( BIT_0 | BIT_4 ) )
    {
        // 두 비트 모두 Set 되었기 때문에 xEventGroupWaitBits() 반환
    }
    else if( ( uxBits & BIT_0 ) != 0 )
    {
        // BIT_0만 설정되었기 때문에 xEventGroupWaitBits() 반환
    }
    else if( ( uxBits & BIT_4 ) != 0 )
    {
        // BIT_4만 설정되었기 때문에 xEventGroupWaitBits() 반환
    }
    else
    {
        // BIT_0 또는 BIT_4가 설정되지 않고 전달되었기 때문에 xEventGroupWaitBits() 반환
    }
}
</pre>





## xEventGroupSetBits
EventBits_t xEventGroupSetBits(EventGroupHandle_t xEventGroup, const EventBits_t uxBitsToSet);
<br>
<b>Description</b> : RTOS 이벤트 그룹 내의 비트를 설정함
<br>
<b>Header</b> : event_groups.h
<br>
<b>Parameter</b>
<br>
　　xEventGroup : xEventGroupCreate() 함수로 호출한 이벤트 그룹 핸들
  <br>
　　uxBitsToSet : 이벤트 그룹 내에서 설정할 비트
<br>
<b>Return</b>
<br>
　　EventBits_t bit : 설정한 이벤트 비트
<br>
<b>Example</b>
<pre>
#define BIT_0	( 1 << 0 )
#define BIT_4	( 1 << 4 )

void aFunction( EventGroupHandle_t xEventGroup )
{
EventBits_t uxBits;

    // xEventGroup에서 BIT_0과 BIT_4를 설정
    uxBits = xEventGroupSetBits(
                              xEventGroup,      // 설정할 이벤트 그룹
                              BIT_0 | BIT_4 );  // 설정할 비트

    if( ( uxBits & ( BIT_0 | BIT_4 ) ) == ( BIT_0 | BIT_4 ) )
    {
        // BIT_0과 BIT_4는 모두 Set된 상태로 유지
    }
    else if( ( uxBits & BIT_0 ) != 0 )
    {
        // BIT_0은 Set된 상태로 유지, BIT_4는 Clear
        // BIT_4를 기다리고 있던 작업이 차단되면서 자동으로 지워질 수 있음
    }
    else if( ( uxBits & BIT_4 ) != 0 )
    {
        // BIT_4는 Set, BIR_0은 Clear
        // Bit_0을 기다리고 있던 작업이 차단되면서 자동으로 지워질 수 있음
    }
    else
    {
        // 두 비트 모두 Set되지 않았고, 기다리고 있던 작업이 차단되면서 자동으로 지워질 수 있음
    }
}
</pre>
