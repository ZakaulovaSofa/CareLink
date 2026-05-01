---
title: Базы данных
sidebar_position: 5
description: Описание сущностей БД 
---

# Сущности системы

[Ссылка на ERD](db.drawio.svg)

## task
**Смысл:** Заявка на помощь  
**Поля:**
- id, SERIAL INT  
- coordinator_id: NOT NULL, INT  
- moderator_id: INT  
- status_id: NOT NULL, INT  
- title: NOT NULL, VARCHAR(200)  
- level: NOT NULL, INT  
- description, TEXT  

---

## file
**Смысл:** Файл, прикрепляемый к заявке  
**Поля:**
- id, SERIAL INT  
- type: NOT NULL, ENUM (photo, video, document)  
- link: NOT NULL, VARCHAR(2048)  

---

## tag
**Смысл:** Тэги для краткого описания заявок  
**Поля:**
- id, SERIAL INT  
- title: NOT NULL, VARCHAR(50)  

---

## task_tag
**Смысл:** Промежуточная таблица для связи task и tag  
**Поля:**
- id, SERIAL INT  
- task_id: NOT NULL, INT  
- tag_id: NOT NULL, INT  

---

## requirement
**Смысл:** Краткие описания требований к волонтёру для выполнения заявки  
**Поля:**
- id, SERIAL INT  
- title: NOT NULL, VARCHAR(100)  

---

## task_requirement
**Смысл:** Промежуточная таблица для связи task и requirement  
**Поля:**
- id, SERIAL INT  
- task_id: NOT NULL, INT  
- requirement_id: NOT NULL, INT  

---

## status
**Смысл:** Словарь с возможными статусами заявок  
**Поля:**
- id, SERIAL INT  
- title: NOT NULL, VARCHAR(50)  

---

## task_status_history
**Смысл:** История изменений статусов заявок (для аналитики)  
**Поля:**
- id, SERIAL INT  
- task_id: NOT NULL, INT  
- status_id: NOT NULL, INT  
- updated_by: NOT NULL, INT  
- updated_at: NOT NULL, TIMESTAMP  

---

## user
**Смысл:** Пользователь (общая информация для всех ролей)  
**Поля:**
- id, SERIAL INT  
- first_name: NOT NULL, VARCHAR(50)  
- last_name: NOT NULL, VARCHAR(50)  
- email: NOT NULL, VARCHAR(254)  
- password_hash: NOT NULL, VARCHAR(254)  

---

## role
**Смысл:** Роли пользователей в системе  
**Поля:**
- id, SERIAL INT  
- title: NOT NULL, VARCHAR(50)  

---

## user_role
**Смысл:** Промежуточная таблица для связи user и role  
**Поля:**
- id, SERIAL INT  
- role_id: NOT NULL, INT  
- user_id: NOT NULL, INT  

---

## coordinator_profile
**Смысл:** Координатор  
**Поля:**
- id, SERIAL INT  
- user_id: UNIQUE, NOT NULL, INT  
- institution, VARCHAR(200)  
- about, TEXT  

---

## moderator_profile
**Смысл:** Модератор  
**Поля:**
- id, SERIAL INT  
- user_id: UNIQUE, NOT NULL, INT  
- about, TEXT  

---

## volunteer_profile
**Смысл:** Волонтёр  
**Поля:**
- id, SERIAL INT  
- user_id: UNIQUE, NOT NULL, INT  
- clearance_level_id: INT  
- experience, TEXT  
- education, VARCHAR(200)  

---

## clearance_level
**Смысл:** Справочник уровней допуска волонтёров  
**Поля:**
- id, SERIAL INT  
- title: NOT NULL  
- description: NOT NULL  

---

## volunteer_level_history
**Смысл:** История изменения уровней допуска волонтёра  
**Поля:**
- id, SERIAL INT  
- volunteer_user_id: NOT NULL  
- new_clearance_level_id: NOT NULL  
- updated_at: NOT NULL  

---

## notification
**Смысл:** Уведомление пользователю (по определённой роли)  
**Поля:**
- id, SERIAL INT  
- user_role_id: NOT NULL, INT  
- type_id: NOT NULL, INT  
- message: NOT NULL, VARCHAR(500)  
- is_read: NOT NULL, BOOLEAN  
- created_at: NOT NULL, TIMESTAMP  

---

## notification_type
**Смысл:** Тип уведомления  
**Поля:**
- id, SERIAL INT  
- title, VARCHAR(50)  

---

## task_volunteer
**Смысл:** Промежуточная таблица для связи task и volunteer  
**Поля:**
- id, SERIAL INT  
- task_id: NOT NULL, INT  
- volunteer_id: NOT NULL, INT  
- assigned_at: TIMESTAMP NOT NULL  
- completed_at: TIMESTAMP  
