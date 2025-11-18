# SQL과 CRUD

### SQL이란?

SQL(Structured Query Language) 은 관계형 데이터베이스(RDBMS)[^1] 에서 데이터를 조회·추가·수정·삭제하기 위한 언어다.

### CRUD란?

CRUD는 데이터베이스의 기본 조작 4가지를 나타내는 약자이다.

| 글자    | 의미     | 동작     | SQL 명령   |
| ----- | ------ | ------ | -------- |
| **C** | Create | 데이터 생성 | `INSERT` |
| **R** | Read   | 데이터 조회 | `SELECT` |
| **U** | Update | 데이터 수정 | `UPDATE` |
| **D** | Delete | 데이터 삭제 | `DELETE` |

### SQL CRUD 예시

예시로 users 테이블이 있다고 하자

``` SQL
CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(100),
  email VARCHAR(100),
  age INT
);

```

#### Create (생성)

새로운 데이터를 DB에 삽입하는 작업

``` SQL
INSERT INTO users (name, email, age)
VALUES ('홍길동', 'hong@example.com', 25);
```

**결과**

| id | name | email                                       | age |
| -- | ---- | ------------------------------------------- | --- |
| 1  | 홍길동  | [hong@example.com](mailto:hong@example.com) | 25  |

- INSERT INTO 테이블명
  - 데이터를 추가할 테이블명을 선택한다.
- (컬럼명)
  - 데이터를 입력할 컬럼명을 선택한다.
  - 컬럼명은 테이블의 컬럼 개수와 입력하는 데이터의 개수가 같을 경우 생략해도 된다.
  - 또한, 기본값이 있거나 널(NULL)값을 저장할 수 있는 컬럼 혹은 숫자가 자동 증가하는 컬럼일 경우도 생략 가능하다.
- VALUES (데이터값)
  - 추가할 데이터를 순차적으로 나열한다.
  - 앞에서 나열한 컬럼명의 개수와 동일하게 데이터의 값을 나열하여야 한다.

#### Read (조회)

저장된 데이터를 읽거나 검색하는 작업

``` SQL
-- 모든 데이터 조회
SELECT * FROM users;

-- 특정 조건으로 조회
SELECT * FROM users WHERE age >= 20;

-- 일부 컬럼만 조회
SELECT name, email FROM users WHERE id = 1;
```

- SELECT 컬럼명
  - 선택한 테이블에 존재하는 컬럼을 선택한다.
  - 컬럼 대신 별표(*)를 사용하면 테이블의 모든 컬럼을 선택할 수 있다.
- FROM 테이블명
  - 데이터를 조회할 테이블을 선택한다.
- WHERE 조건
  - 조회할 데이터의 조건이 존재한다면 조건을 추가한다.
  - 이 구문은 필수가 아니며 조건이 존재하지 않을 경우에는 생략해도 된다.

#### Update (수정)
기존 데이터를 변경하는 작업
WHERE 절을 써야 특정 행만 수정 가능!

``` SQL
UPDATE users
SET age = 26
WHERE id = 1;
```

- UPDATE 테이블명
  - 데이터를 갱신할 테이블을 선택한다.
- SET 컬럼명1 = 데이터값1
  - 선택한 컬럼에 기재한 데이터 값으로 데이터를 갱신한다.
  - WHERE 절의 조건이 없을 경우, 테이블의 모든 행이 갱신될 수 있으므로 주의하여야 한다.
- WHERE 조건
  - 갱신할 데이터의 조건이 존재한다면 조건을 추가한다.
  - 이 구문은 필수가 아니지만, 가급적 사용을 권장한다.

#### Delete (삭제)
데이터를 삭제하는 작업
WHERE 절을 빼면 모든 행이 삭제된다.

```SQL
DELETE FROM users
WHERE id = 1;
```

- DELETE FROM 테이블명
  - 데이터를 삭제할 테이블을 선택한다.
  - **주의** : 조건을 작성하지 않고 위의 쿼리만 실행할 경우 테이블의 모든 내용이 지워지니 조심해야 한다.
- WHERE 조건
  - 삭제할 데이터의 조건이 존재한다면 조건을 추가한다.
  - 이 구문은 필수가 아니지만 테이블의 모든 데이터가 삭제되기 때문에 가능한 필수적으로 사용을 권장한다.

##### CRUD는 DB 트랜잭션의 기본 단위

| 개념                  | 의미                        |
| ------------------- | ------------------------- |
| **A (Atomicity)**   | 모두 실행되거나 전혀 실행되지 않음 (원자성) |
| **C (Consistency)** | 제약조건 유지 (무결성)             |
| **I (Isolation)**   | 동시 실행 시 간섭 없음             |
| **D (Durability)**  | 성공한 트랜잭션은 영구 반영           |

이는 뒤에서 더 자세하게 다룰 예정이다.

#### CRUD 성능 관련 고려사항

| 항목                     | 설명                                      |
| ---------------------- | --------------------------------------- |
| **인덱스(Index)**         | `SELECT`, `UPDATE`, `DELETE` 시 검색 속도 향상 |
| **트랜잭션(Transaction)**  | 여러 CRUD를 하나로 묶어 원자성 보장                  |
| **락(Lock)**            | 동시 수정 충돌 방지                             |
| **정규화(Normalization)** | 데이터 중복 최소화로 효율적 CRUD 가능                 |
| **JOIN**               | 여러 테이블을 읽는 Read 성능 영향                   |

#### ORM (객체-관계 매핑)에서의 CRUD

Spring JPA, Hibernate, Django ORM, SQLAlchemy 같은 ORM[^2]에서도 CRUD는 거의 1:1 대응된다.

| CRUD   | JPA 메서드 예시                         |
| ------ | ---------------------------------- |
| Create | `save(entity)`                     |
| Read   | `findById(id)`, `findAll()`        |
| Update | `save(entity)` (변경감지로 자동 업데이트)     |
| Delete | `delete(entity)`, `deleteById(id)` |

# Transaction (트랜잭션)

### 트랜잭션이란?
트랜잭션은 “DB에서 수행되는 작업들을 하나의 묶음(단위)으로 보고, 그 묶음이 전부 성공하거나 전부 실패하도록 보장하는 메커니즘”이다.

### 트랜잭션이 필요한 이유



[^1]: 관계형 데이터베이스는 데이터를 행과 열로 이루어진 테이블 형태로 저장하고, 이러한 테이블들이 키(key)와 외래 키(foreign key)를 통해 서로 연결되어 관계를 맺는 데이터베이스입니다.
[^2]: ORM(Object-Relational Mapping)은 객체 지향 프로그래밍 언어의 객체와 관계형 데이터베이스의 테이블 간 데이터를 자동으로 연결하고 변환해주는 기술입니다. 이를 통해 개발자는 SQL 쿼리 대신 객체 지향적인 코드로 데이터베이스 작업을 수행할 수 있게 되어, 개발 과정이 단순해지고 생산성이 향상됩니다. 
