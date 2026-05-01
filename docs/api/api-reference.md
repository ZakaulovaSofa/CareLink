# Описание API

Логика работы системы со стороны координатора:

-   Координатор может зайти в свой профиль (`GET /api/coordinators/{coordinatorId}`) и отредактировать информацию о себе (`PATCH /api/coordinators/{coordinatorId}`)

-   Координатор может получить список своих уже завершённых заявок в профиле при помощи `GET /api/my_tasks/status=complete_succesfully`

-   Координатор может создавать (`POST /api/my_tasks`) и удалять свои заявки (`PATCH /api/my_tasks/{taskId}`)

-   Координатор может увидеть список всех своих заявок (с любым статусом), перейдя в раздел "Мои заявки" при помощи `GET /api/my_tasks/?offset=X&limit=Y` (поддержка пагинации на странице), а далее при желании отфильтровать по статусу и/или уровню сложности.

-   Координатор может просматривать информацию о конкретной заявке (`GET /api/my_tasks/{taskId}`) и получать информацию о волонтёре по заявке (`GET /api/volunteers/{volunteerId}`)

---

## Мои заявки (`/api/my_tasks`)

|Метод|Endpoint|Описание| |-|-|-| |GET|`/api/my_tasks`|Получение списка заявок с пагинацией и фильтрацией (status, level)| |POST|`/api/my_tasks`|Создание новой заявки|

Для поиска и фильтрации можно было бы использовать POST-запрос, однако в случае данной ИС подойдёт и GET, так как нет большого количества параметров запроса, то есть URL будет выглядеть адекватно и с пагинацией, и с фильтрацией по статусу и уровню сложности, а пользователю так будет удобнее работать с ссылками при необходимости.

---

## Конкретная заявка (`/api/my_tasks/{taskId}`)

|Метод|Endpoint|Описание| |-|-|-| |GET|`/api/my_tasks/{taskId}`|Получение детальной информации о заявке| |PATCH|`/api/my_tasks/{taskId}`|Досрочное закрытие заявки (смена статуса на `completed_early`)|

---

## Волонтёры (`/api/volunteers`)

|Метод|Endpoint|Описание| |-|-|-| |GET|`/api/volunteers/{volunteerId}`|Получение информации о волонтёре|

---

## Координаторы (`/api/coordinators`)

|Метод|Endpoint|Описание| |-|-|-| |GET|`/api/coordinators/{coordinatorId}`|Получение профиля координатора| |PATCH|`/api/coordinators/{coordinatorId}`|Обновление профиля координатора|

---

# Авторизация

|Тип|Описание| 
|-|-| 
|Bearer Token (JWT)|Требуется для всех endpoints|

---

# Фильтрация и пагинация

|Параметр|Тип|Описание| 
|-|-|-| 
|limit|integer|Количество записей (1–100, по умолчанию 30)| 
|offset|integer|Смещение (по умолчанию 0)| 
|status|enum|Фильтр по статусу заявки| 
|level|integer|Фильтр по уровню сложности (1–10)|

---

# Статусы заявок (`TaskStatus`)

|Значение|Описание| 
|-|-| 
|requires_review|Требует проверки| 
|searching_volunteer|Поиск волонтёра| 
|in*progress|В работе| 
|completed_early|Завершена досрочно| 
|completed_by_volunteer|Завершена волонтёром| 
|completed_successfully|Закрыта (успешно завершена)|

---

# Возможные ошибки

|Код|Описание| 
|-|-| 
|401|Не авторизован| 
|403|Нет доступа| 
|404|Ресурс не найден| 
|500|Ошибка сервера|

---

# Код OpenAPI
```yaml
openapi: 3.0.2
info:
    title: Carelink API
    version: 1.0.0
    description: API для работы с системой координации волонтёров
tags:
  - name: Мои заявки
  - name: Волонтёры
  - name: Координаторы
paths:
  /api/my_tasks:
    get:
      tags: ["Мои заявки"]
      summary: Получение списка заявок с возможностью пагинации и фильтрации
      security:
        - bearerAuth: []
      parameters: 
        - name: limit
          in: query
          description: Число записей на странице
          required: false
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 30
            example: 30
        - name: offset
          in: query
          description: Смещение
          required: false
          schema: 
            type: integer
            minimum: 0
            default: 0
            example: 0
        - name: status
          in: query
          description: Статус заявки
          required: false
          schema: 
            $ref: '#/components/schemas/TaskStatus'
        - name: level
          in: query
          description: Уровень сложности
          required: false
          schema: 
            type: integer
            minimum: 1
            maximum: 10
            example: 7
      responses: 
        '200':
          description: Список заявок
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PaginatedTasksResponse'
        '401':
          description: Пользователь не авторизован
        '403':
          description: Пользователь не имеет прав для просмотра страницы
        '500':
          description: Ошибка сервера
    post:
      summary: Создание новой заявки
      tags: ["Мои заявки"]
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TaskCreateRequest'
      responses:
        '201':
          description: Заявка создана
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Task'
        '401':
          description: Пользователь не авторизован
        '403':
          description: Пользователь не имеет прав для просмотра страницы
        '500':
          description: Ошибка сервера
  /api/my_tasks/{taskId}:
    get:
      summary: Получение детальной информации о заявке
      tags: ["Мои заявки"]
      security:
        - bearerAuth: []
      parameters:
        - name: taskId
          in: path
          required: true
          schema:
            type: integer
      responses:
        '200':
          description: Детальная информация о заявке
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Task'
        '401':
          description: Пользователь не авторизован
        '403':
          description: Пользователь не имеет прав для просмотра страницы
        '404':
          description: Ресурс не найден
        '500':
          description: Ошибка сервера
    patch:
      summary: Изменение заявки (досрочное закрытие)
      tags: ["Мои заявки"]
      security:
        - bearerAuth: []
      parameters:
        - name: taskId
          in: path
          required: true
          schema:
            type: integer
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TaskUpdateRequest'
      responses:
        '200':
          description: Заявка обновлена
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Task'
        '401':
          description: Пользователь не авторизован
        '403':
          description: Пользователь не имеет прав для просмотра страницы
        '404':
          description: Ресурс не найден
        '500':
          description: Ошибка сервера
  /api/volunteers/{volunteerId}:  
    get:
      summary: Получение информации о волонтёре
      tags: ["Волонтёры"]
      security:
        - bearerAuth: []
      parameters:
        - name: volunteerId
          in: path
          required: true
          schema:
            type: integer
      responses:
        '200':
          description: Детальная информация о волонтёре по заявке
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Volunteer'
        '401':
          description: Пользователь не авторизован
        '403':
          description: Пользователь не имеет прав для просмотра страницы
        '404':
          description: Ресурс не найден
        '500':
          description: Ошибка сервера
  /api/coordinators/{coordinatorId}:
    get:
      summary: Получение профиля координатора
      tags: ["Координаторы"]
      security:
        - bearerAuth: []
      parameters:
        - name: coordinatorId
          in: path
          required: true
          schema:
            type: integer
      responses:
        '200':
          description: Детальная информация о координаторе
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Coordinator'
        '401':
          description: Пользователь не авторизован
        '403':
          description: Пользователь не имеет прав для просмотра страницы
        '404':
          description: Ресурс не найден
        '500':
          description: Ошибка сервера
    patch:
      summary: Изменение профиля координатора
      tags: ["Координаторы"]
      security:
        - bearerAuth: []
      parameters:
        - name: coordinatorId
          in: path
          required: true
          schema:
            type: integer
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CoordinatorUpdateRequest'
      responses:
        '200':
          description: Профиль обновлен
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Coordinator'
        '401':
          description: Пользователь не авторизован
        '403':
          description: Пользователь не имеет прав для просмотра страницы
        '404':
          description: Ресурс не найден
        '500':
          description: Ошибка сервера

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
  schemas:
    ErrorResponse:
      type: object
      required:
        - error
      properties:
        error:
          type: string
    Tag:
      type: object
      properties:
        id:
          type: integer
          example: 3
        title:
          type: string
          example: "Уборка"
    Requirement:
      type: object
      properties:
        id:
          type: integer
          example: 1
        title:
          type: string
          example: "Медицинское образование"
    File:
      type: object
      properties:
        id: 
          type: integer
          example: 23
        type:
          type: string
          enum: [photo, video, document]
          example: photo
        link:
          type: string
          example: /photos/IrinaIvanova.png
    TaskStatus:
      type: string
      enum:
        - requires_review
        - searching_volunteer
        - in_progress
        - completed_early
        - completed_by_volunteer
        - completed_successfully
    Task:
      type: object
      required:
        - id
        - coordinator_id
        - title
        - level
        - status
      properties:
        id:
          type: integer
          example: 2014
        coordinator_id:
          type: integer
          example: 231
        title:
          type: string
          example: "Помощь Ирине Ивановне"
        level:
          type: integer
          example: 2
        tags:
          type: array
          items:
            $ref: '#/components/schemas/Tag'
        volunteer_requirements:
          type: array
          items:
            $ref: '#/components/schemas/Requirement'
        description:
          type: string
          example: "Требуется помощь в обезболивании и уборке"
        files:
          type: array
          items:
            $ref: '#/components/schemas/File'
        status:
          $ref: '#/components/schemas/TaskStatus'
    PaginatedTasksResponse:
      type: object
      properties:
        count:
          type: integer
          example: 100
        limit:
          type: integer
          example: 30
        offset:
          type: integer
          example: 0
        results:
          type: array
          items:
            $ref: '#/components/schemas/Task'
    TaskCreateRequest:
      type: object
      required:
        - title
        - level
      properties:
        title:
          type: string
        level:
          type: integer
        tags:
          type: array
          items:
            type: integer
          example: [3, 5]
        volunteer_requirements:
          type: array
          items:
            type: integer
          example: [1]
        description:
          type: string
    TaskUpdateRequest:
      type: object
      properties:
        status:
          type: string
          enum:
            - completed_early
    ClearanceLevel:
      type: string
      enum:
        - undefined
        - on_moderation
        - without_permission
        - base
        - extended
        - advanced
        - expert
    Volunteer:
      type: object
      required: 
        - id
        - first_name
        - last_name
        - clearance_level
        - completed_tasks
      properties: 
        id:
          type: integer
          example: 234
        first_name:
          type: string
          example: "Иван"
        last_name:
          type: string
          example: "Петров"
        clearance_level:
          $ref: '#/components/schemas/ClearanceLevel'
        experience:
          type: string
          example: "Имеется опыт работы в детских больницах"
        education:
          type: string
          example: "Московский государственный медицинский университет, педиатрия"
        completed_tasks:
          type: array
          items:
            type: integer
          example: [1, 5, 12]
    Coordinator:
      type: object
      required: 
        - id
        - first_name
        - last_name
        - institution
      properties: 
        id:
          type: integer
          example: 7856
        first_name:
          type: string
          example: "Николай"
        last_name:
          type: string
          example: "Кузьмин"
        institution:
          type: string
          example: "Хоспис №3"
        about:
          type: string
          example: "Изучал медицину в ИГМУ, работаю в хосписе на протяжении последних 7 лет"
    CoordinatorUpdateRequest:
      type: object
      properties:
        first_name:
          type: string
        last_name:
          type: string
        institution:
          type: string
        about: 
          type: string
  


servers:
  # Added by API Auto Mocking Plugin
  - description: SwaggerHub API Auto Mocking
    url: https://virtserver.swaggerhub.com/isu-366/first_api/1.0.0
```