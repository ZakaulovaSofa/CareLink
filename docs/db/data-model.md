## Database diagram

Открыть диаграмму:
- файл `db.drawio.svg`
- или через VS Code (Draw.io Integration)

## Модели базы данных

### Users (Пользователи)
- id: уникальный идентификатор
- name: имя пользователя
- email: email
- password_hash: хэш пароля
- role: роль (пациент, врач и т.д.)
- created_at, updated_at: timestamps

### Patients (Пациенты)
- id: уникальный идентификатор
- user_id: ссылка на users
- medical_record_number: номер медицинской карты
- date_of_birth: дата рождения
- gender: пол
- address: адрес
- phone: телефон
- emergency_contact: контакт в экстренных случаях
- created_at, updated_at

### Doctors (Врачи)
- id: уникальный идентификатор
- user_id: ссылка на users
- specialization: специализация
- license_number: номер лицензии
- hospital_affiliation: affiliation с больницей
- created_at, updated_at

### Appointments (Назначения)
- id: уникальный идентификатор
- patient_id: ссылка на patients
- doctor_id: ссылка на doctors
- appointment_date: дата назначения
- status: статус
- notes: заметки
- created_at, updated_at

### Medical Records (Медицинские записи)
- id: уникальный идентификатор
- patient_id: ссылка на patients
- doctor_id: ссылка на doctors
- record_date: дата записи
- diagnosis: диагноз
- treatment: лечение
- medications: лекарства
- notes: заметки
- created_at, updated_at

### Prescriptions (Рецепты)
- id: уникальный идентификатор
- patient_id: ссылка на patients
- doctor_id: ссылка на doctors
- medication_name: название лекарства
- dosage: дозировка
- frequency: частота
- start_date: дата начала
- end_date: дата окончания
- notes: заметки
- created_at, updated_at
