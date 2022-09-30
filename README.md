# FreeRTOS
FreeRTOS API Reference 정리 Repository


## xTaskCreate
BaseType_t xTaskCreate(TaskFunction_t pvTaskCode, const char* const pcName, configSTACK_DEPTH_TYPE usStackDepth, 
<br>
　　　　　　　　　　　void *pvParameters, UBaseType_t uxPriority, TaskHandle_t *pxCreatedTask);
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




## void vTaskSuspend(TaskHandle_t xTaskToSuspend);
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


