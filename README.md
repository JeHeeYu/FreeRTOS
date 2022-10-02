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
　　xTask : 재시작할 태스크 핸들러
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
　　xSemaphore : 생성된 세마포어의 핸들러
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
