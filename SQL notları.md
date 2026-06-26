# SQL notları

## Add/Insert
```sql
    INSERT INTO demo (ID, Name, Hint) VALUES (29, 'serkan', 'piano');
```
## Delete
```sql
    DELETE FROM demo WHERE name = 'serkan'
```
## Update
```sql
    UPDATE demo SET id = 30 WHERE name = 'Serkan';
```
- id yi 30 olarak değiştirir.

## Filtering
```sql
    SELECT * FROM demo WHERE name = 'Serkan';
```
- Name sütunundaki Serkanları getirir.
---
```sql
    SELECT * FROM demo WHERE name LIKE  'S%';
```
- name sütununda S ile başlayan satırları gösterir.

- % başa konursa S ile bitenleri gösterir.
    
    