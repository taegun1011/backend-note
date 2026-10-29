## 중복 레코드

- GROUP BY + HAVING
  ```sql
  -- 이메일 계정이 여러 개인 고객
  SELECT email, COUNT(*) AS count
  FROM customers
  GROUP BY email
  HAVING COUNT(*) > 1;
  ```

## 차집합

- NOT EXISTS
  - **서브쿼리에서 조회 결과가 하나도 존재하지 않는** 레코드를 반환한다

  ```sql
  -- 주문을 하지 않은 고객
  SELECT c.id
  FROM customers c
  WHERE NOT EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.id
  )
  ```

- LEFT JOIN + IS NULL

  ```sql
  -- 재고가 없는 상품
  SELECT p.id, p.name
  FROM products p
  LEFT JOIN inventory i
    ON p.id = i.product_id
  WHERE i.product_id IS NULL;
  ```

- NOT EXISTS와 LEFT JOIN + IS NULL은 유사한 기능을 구현할 수 있으므로 옵티마이저 실행 계획을 확인해서 더 나은 쪽을 선택하는 게 좋다

## 전체 순위

- LIMIT

  ```sql
  -- 최상위 연봉자 3명
  SELECT id, name, salary
  FROM employees
  ORDER BY salary DESC
  LIMIT 3;
  ```

## 그룹 내 순위

- **ROW_NUMBER**
  - SELECT 절에서 사용하는 집계함수
  - ROW_NUMBER() OVER (PARTITION BY a ORDER BY b)
    - 조회 결과에서 a 컬럼끼리 (그룹) b 컬럼으로 (정렬 기준) 순위를 매긴다

  ```sql
  -- 부서별 최고 연봉자 2명
  SELECT id, name, department_id, salary, rn
  FROM (
    SELECT e.*,
      ROW_NUMBER() OVER
      (PARTITION BY department_id ORDER BY salary DESC, employee_id) rn
    FROM employees e
  )
  WHERE rn <= 2;
  ```

- 윈도우 함수 비교

| salary | ROW_NUMBER | RANK | DENSE_RANK |
| ------ | ---------- | ---- | ---------- |
| 120000 | 1          | 1    | 1          |
| 110000 | 2          | 2    | 2          |
| 100000 | 3          | 3    | 3          |
| 100000 | 4          | 3    | 3          |
| 90000  | 5          | 5    | 4          |
  
  - ROW_NUMBER: 순서에 집중
  - RANK: 순위에 집중
  - DENSE_RANK: 공동 순위의 공백 제거

## 누적합

- SUM() OVER

  ```sql
  -- 일자별 주문량 누적합 계산
  SELECT order_date, amount,
    SUM(amount) OVER (
      ORDER BY order_date, id
    ) AS running_total
  FROM orders;
  ```

## 이동평균

## 전/후 값 찾기

## 계층 구조 다루기

## 페이지네이션

## UPSERT
