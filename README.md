# 📘 MyBatisProject

간단한 **학생 / 강좌 / 수강(Enrollment) 관리 웹 애플리케이션**입니다.  
Spring MVC(서블릿) + JSP + MyBatis + MySQL 기반으로 제작되었습니다.


## 🛠 주요 기능

1. **학생(Student) 관리**  
   - 목록 / 검색 (이름, 학번)
   - 등록, 수정, 삭제, 상세보기
2. **강좌(Course) 관리**  
   - 목록 / 검색 (강좌명, 코드)
   - 등록, 수정, 삭제, 상세보기(수강자 목록)
3. **수강(Enrollment) 관리**  
   - 학생 상세에서 수강신청 및 취소
   - `UNIQUE(student_id, course_id)` 제약으로 중복 신청 방지

---

## 🗂 목차
1. [⚙ 환경 설정](#-환경-설정)
2. [🛢 DB 생성 및 계정 설정](#-db-생성)
3. [🌐 엔드포인트](#-엔드포인트)
4. [🖥 화면 구성](#-화면-구성)
5. [🎥시연 영상](#-시연-영상)

---

## ⚙ 환경 설정
- **JDK**: 8+ (권장 11)
- **Tomcat**: 9.x
- **MySQL**: 8.x
- **빌드 도구**: Gradle
- **IDE**: IntelliJ
- **서버 포트**: 8080  

---

## 🛢 DB 생성 

### DB 생성
<details><summary>SQL 보기</summary>

```sql
create database mybatisdb;
use mybatisdb;
CREATE TABLE student (
                         id BIGINT PRIMARY KEY AUTO_INCREMENT,
                         student_no VARCHAR(20) NOT NULL UNIQUE,
                         name VARCHAR(50) NOT NULL,
                         email VARCHAR(100) UNIQUE,
                         dept VARCHAR(50),
                         created_at DATETIME DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB;

CREATE TABLE course (
                        id BIGINT PRIMARY KEY AUTO_INCREMENT,
                        code VARCHAR(20) NOT NULL UNIQUE,
                        title VARCHAR(100) NOT NULL,
                        professor VARCHAR(50),
                        credit TINYINT NOT NULL DEFAULT 3,
                        created_at DATETIME DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB;

CREATE TABLE enrollment (
                            id BIGINT PRIMARY KEY AUTO_INCREMENT,
                            student_id BIGINT NOT NULL,
                            course_id BIGINT NOT NULL,
                            enrolled_at DATETIME DEFAULT CURRENT_TIMESTAMP,
                            CONSTRAINT uk_enroll UNIQUE (student_id, course_id),
                            CONSTRAINT fk_enroll_student FOREIGN KEY(student_id)
                                REFERENCES student(id) ON DELETE CASCADE,
                            CONSTRAINT fk_enroll_course FOREIGN KEY(course_id)
                                REFERENCES course(id) ON DELETE CASCADE
) ENGINE=InnoDB;

-- 추천 인덱스
CREATE INDEX idx_student_name ON student(name);
CREATE INDEX idx_course_title ON course(title);
```
</details>

## DB 샘플데이터
<details><summary>SQL 보기</summary>

```
INSERT INTO student(student_no,name,email,dept) VALUES
('20250001','김하나','hana@example.com','컴퓨터공학'),
('20250002','이둘','dul@example.com','정보통신'),
('20250003','박셋','set@example.com','소프트웨어');

INSERT INTO course(code,title,professor,credit) VALUES
('CS101','프로그래밍 기초','최교수',3.50),
('CS201','자료구조','김교수',3.40),
('CS301','데이터베이스','이교수',3.35);
```

</details>

## 🌐 엔드포인트
| Method | Path                    | 설명            | 파라미터                          |
| ------ | ----------------------- | ------------- | ----------------------------- |
| GET    | `/students`             | 학생 목록         | `q`, `page`, `size`           |
| GET    | `/students/new`         | 학생 등록 폼       |                               |
| POST   | `/students/new`         | 학생 등록 처리      | `studentNo,name,email,dept`   |
| GET    | `/students/{id}`        | 학생 상세         |                               |
| POST   | `/students/{id}/delete` | 학생 삭제         |                               |
| GET    | `/courses`              | 강좌 목록         | `q`, `page`, `size`           |
| POST   | `/courses/new`          | 강좌 등록 처리      | `code,title,professor,credit` |
| GET    | `/courses/{id}`         | 강좌 상세(수강자 목록) |                               |
| POST   | `/enrollments`          | 수강신청          | `studentId,courseId`          |
| POST   | `/enrollments/cancel`   | 수강취소          | `studentId,courseId`          |

## 🖥 화면 구성
- students/list.jsp : 학생 목록
- students/detail.jsp : 학생 상세 + 수강신청 UI
- courses/courseList.jsp : 강좌 목록
- courses/detail.jsp : 강좌 상세 + 수강자 목록
- common/header.jsp, common/nav.jsp, common/footer.jsp : 공통 레이아웃

## 🎥 시연 영상

[[시연 영상 주소]](assignment0905%20시연영상.mp4)


