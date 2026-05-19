+++
title = "NestJS 입문 — 구조화된 Node.js 백엔드 프레임워크"
date = "2024-06-12"
description = "NestJS는 모듈, 컨트롤러, 프로바이더라는 세 가지 빌딩 블록으로 백엔드를 체계적으로 조립하는 Node.js 프레임워크다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> NestJS는 모듈, 컨트롤러, 프로바이더라는 세 가지 빌딩 블록으로 백엔드를 체계적으로 조립하는 Node.js 프레임워크다.

---

## NestJS를 왜 쓰는지 감 잡기

Express만으로 백엔드를 만들면 파일 구조와 의존성 관리를 모두 직접 결정해야 한다. 프로젝트가 커질수록 어디에 무엇을 두어야 하는지 규칙이 없어 코드가 뒤엉키기 쉽다.

NestJS는 Angular에서 영감을 받아 만들어진 프레임워크로, "이런 경우엔 이 파일에 이 코드를 넣어라"는 규칙을 미리 정해둔다. TypeScript를 기본으로 지원하며, 의존성 주입 컨테이너가 내장되어 서비스 간 연결을 자동으로 처리한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 요청 도착 → Controller가 받음 → Provider(Service)에서 처리 → 응답 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Module | 관련 기능을 하나로 묶는 단위. 기능별로 폴더처럼 분리한다. |
| Controller | HTTP 요청을 받아 적절한 서비스로 넘겨주는 진입점 |
| Provider (Service) | 실제 비즈니스 로직이 담기는 곳. 컨트롤러가 직접 호출한다. |
| Decorator | `@Controller()`, `@Get()` 같이 클래스나 메서드 위에 붙이는 메타 정보 |
| Dependency Injection | 필요한 서비스를 직접 생성하지 않고 NestJS가 알아서 주입해주는 방식 |

## 예를 들어 설명하면

도서 관리 API를 만든다고 하면 세 파일로 역할을 나눈다.

```ts
// books.module.ts — 기능 묶음
@Module({
  controllers: [BooksController],
  providers: [BooksService],
})
export class BooksModule {}

// books.controller.ts — 요청 진입점
@Controller('books')
export class BooksController {
  constructor(private readonly booksService: BooksService) {}

  @Get()
  findAll(): string {
    return this.booksService.findAll();
  }
}

// books.service.ts — 로직 처리
@Injectable()
export class BooksService {
  findAll(): string {
    return 'all books';
  }
}
```

`BooksController`는 `BooksService`를 생성자에 선언만 하면 NestJS가 자동으로 인스턴스를 주입한다. 직접 `new BooksService()`를 쓸 필요가 없다.

## 이 단계에서 중요한 판단 기준

새 기능을 추가할 때 "이 코드가 Controller인지 Service인지" 먼저 판단하면 파일 위치가 자연스럽게 결정된다.

## 한 줄 요약 — 이것만 기억하면 된다

**NestJS는 Module-Controller-Provider 세 역할로 백엔드 코드를 정해진 자리에 배치하게 강제하는 구조화된 프레임워크다.**

## 나중에 더 깊게 들어가면

- Guards, Interceptors, Pipes — 요청 처리 파이프라인 심화
- NestJS와 Prisma 또는 TypeORM 연동
- 마이크로서비스 모드와 메시지 큐 연동

---

**원본:** [NestJS Introduced](https://memoryhub.tistory.com/286)
