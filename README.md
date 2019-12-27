# Протокол обмена с windows службой диспенсера карт.

    Служба принимает запросы в формате JSON на localhost по tcp сокету на порт, который будет задан в настройках.
    
## Формат запросов

{  
    request: [RequestType](#RequestType),  
    args: object // если требует запрос  
}

## Формат ответа

{  
    result: [ServiceResult](#ServiceResult),  // результат выполнения запроса службой  
    resultValue: object // если есть результирующее значение  
}

### RequestType
- 0 - запроc состояния сенсоров диспенсера  
- 1 - переместить карту в заданную позицию
- 2 - создать карту гостя

### ServiceResult
- 0 - Успех
- 1 - Неверный формат входных данных
- 2 - Один из параметров не задан или задан неверно
- 3 - Не удалось подключиться к диспенсеру по заданному COM порту
- 4 - Нет карты в позиции для записи
- 5 - Некорректная карта
- 6 - Внутренняя ошибка диспенсера //если возвращена эта ошибка, то в поле ответа resultValue будет лежать код этой ошибки, [Коды ошибок диспенсера](#коды-ошибок-диспенсера) 

#### Запрос состояния сенсоров

    Не требует входных параметров.
    
    Если результат выполнения операции - Успех, в поле овтета resultValue будет json объект:  
    {  
        hasCardInRfPosition: bool,  
        hasCardInICPosition: bool,  
        hasCardInFrontHoldingPosition: bool,  
        hasCardInCaptureBox: bool,  
        hasCardInCardsBox: bool,  
        hasInsufficientAmountInCardsBox: bool  
    }
    
#### Запрос перемещения карты

    В поле запроса args должен быть объект:  
    {  
        moveMode: MoveMode,  
    }
    
##### MoveMode
- 1 - ToFront
- 2 - ToFrontHoldingPosition
- 3 - ToICPosition
- 4 - ToRFPosition
- 5 - ToCaptureBox

#### Запрос создания карты гостя

    Для создания карты гостя необходимо, чтобы карта находилась в RF позиции.
    
    В поле args должен быть объект:  
    {  
        hotelKey: int, // [100_000, 1_000_000],  
        cardNumber: int, // [100_000, 1_000_000],  
        building: int, // [1, 16]
        rooms: [int], //[101, 9999]  
        isLux: bool,  
        innerRooms: [int], // [1, 5]  
        mutualRooms: [int], // [1, 8]  
        canForceOpen: bool, // может открывать ригель
        from: DateTime, // в формате ISO 8601 с точностью до секунд  
        to: DateTime, // в формате ISO 8601 с точностью до секунд
    }
    
### Коды ошибок диспенсера

- 0x0000 - AD3_S_SUCCESS				
- 0x3000 - AD3_E_PORT_UNAVAILABLE		
- 0x3002 - AD3_E_DEV_NOT_RECOGNIZED	
- 0x3010 - AD3_E_COMM_ERROR			
- 0x3011 - AD3_E_COMM_TIMEOUT			
- 0x3021 - AD3_E_UNKNOWN_ERROR			
- 0x4010 - AD3_E_MESSAGE_TOO_LONG		
- 0x4020 - AD3_E_NO_MEMORY				
- 0x4000 - AD3_E_BUFFER_TOO_SMALL		
- 0x4001 - AD3_E_INVALID_HANDLE		
- 0x4E00 - AD3_E_UNDEFINED_COMMAND		
- 0x4E01 - AD3_E_INVALID_PARAMETER		
- 0x4E02 - AD3_E_DISABLED_COMMAND		
- 0x4E03 - AD3_E_UNSUPPORTED_COMMAND	
- 0x4E05 - AD3_E_CONTACT_NO_RELEASE	
- 0x4E10 - AD3_E_CARD_JAMMED			
- 0x4E12 - AD3_E_SENSOR_ABNORMALITY	
- 0x4E13 - AD3_E_TOO_LONG_CARD			
- 0x4E14 - AD3_E_TOO_SHORT_CARD		
- 0x4E40 - AD3_E_CARD_WITHDRAWN		
- 0x4E41 - AD3_E_IC_SOLENOID_ERROR		
- 0x4E43 - AD3_E_CANT_MOVED_TO_IC_POS	
- 0x4E45 - AD3_E_CARD_POSITION_CHANGE	
- 0x4E50 - AD3_E_COUNTER_OVERFLOW		
- 0x4E51 - AD3_E_MOTOR_ABNORMALITY		
- 0x4E60 - AD3_E_POWER_SHORT			
- 0x4E61 - AD3_E_ICC_ACTIVATION_ERROR	
- 0x4E65 - AD3_E_ICC_NOT_ACTIVATED		
- 0x4E66 - AD3_E_UNSUPPORTED_ICC		
- 0x4E67 - AD3_E_ICC_RECEPTION_ERROR	
- 0x4E68 - AD3_E_ICC_COMM_TIMEOUT		
- 0x4E69 - AD3_E_MISMATCH_EMV			
- 0x4EA1 - AD3_E_CARD_BOX_EMPTY		
- 0x4EA2 - AD3_E_CAPTURE_BOX_FULL		
- 0x4EB0 - AD3_E_WAITING_FOR_RESET		
- 0x6F00 - AD3_E_COMMAND_FAILURE		
- 0x6F01 - AD3_E_DISAGREEMENT_OF_VC	
- 0x6F02 - AD3_E_CARD_LOCKED			
- 0x6B00 - AD3_E_ADDRESS_OVERFLOW		
- 0x6700 - AD3_E_LENGTH_OVERFLOW	
