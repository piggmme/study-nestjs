# NestJS

## Module, Controller, Service

NestJS에서 서비스(Service), 컨트롤러(Controller), **모듈(Module)**은 애플리케이션의 구조를 구성하는 핵심 구성 요소입니다. 각각의 역할과 기능이 명확하게 분리되어 있어, 애플리케이션을 유지보수하기 쉽게 만들어줍니다.

### 1. 컨트롤러(Controller)
컨트롤러는 사용자 요청을 처리하는 역할을 합니다.

- 역할
  - HTTP 요청(예: GET, POST, PUT, DELETE)을 수신하고 응답을 반환합니다.
  - 사용자가 전송한 데이터를 검증하거나 서비스로 전달합니다.
  - 주로 사용자와 애플리케이션 간의 입출력 처리를 담당합니다.

```ts
import { Controller, Get, Post, Body } from '@nestjs/common';

@Controller('users') // '/users' 경로에 대한 요청을 처리
export class UserController {
  constructor(private readonly userService: UserService) {} // 서비스 주입

  @Get()
  getAllUsers() {
    return this.userService.findAll(); // 서비스 호출
  }

  @Post()
  createUser(@Body() userData: any) {
    return this.userService.create(userData); // 서비스 호출
  }
}
```


### 2. 서비스(Service)
서비스는 비즈니스 로직을 처리하는 역할을 합니다.

- 역할
  - 컨트롤러와 분리된 상태에서 실제 핵심 비즈니스 로직을 처리합니다.
  - 데이터베이스 작업, 외부 API 호출, 복잡한 연산 등을 수행합니다.
  - 컨트롤러는 서비스에 요청을 위임하고, 서비스는 결과를 반환합니다.

- 특징
  - NestJS의 의존성 주입(Dependency Injection) 시스템을 사용하여 다른 클래스에서 쉽게 재사용할 수 있습니다.
  - @Injectable() 데코레이터로 등록됩니다.

```ts
import { Injectable } from '@nestjs/common';

@Injectable() // 의존성 주입 가능하도록 선언
export class UserService {
  private users = []; // 예시용 메모리 데이터베이스

  findAll() {
    return this.users; // 모든 사용자 반환
  }

  create(user: any) {
    this.users.push(user); // 사용자 추가
    return user;
  }
}
```

### 3. 모듈(Module)
모듈은 애플리케이션의 구조를 구성하는 단위입니다.

- 역할:
  - 관련된 컨트롤러와 서비스를 묶어서 관리합니다.
  - NestJS 애플리케이션을 모듈 단위로 분리하여 큰 애플리케이션도 관리하기 쉽게 만듭니다.
  - 애플리케이션의 모든 기능은 최소 하나의 모듈에 속합니다.

- 기본 모듈:
  - NestJS 애플리케이션은 항상 기본적으로 AppModule에서 시작됩니다.

```ts
import { Module } from '@nestjs/common';
import { UserController } from './user.controller';
import { UserService } from './user.service';

@Module({
  controllers: [UserController], // 이 모듈에서 사용할 컨트롤러 등록
  providers: [UserService],      // 이 모듈에서 사용할 서비스 등록
})
export class UserModule {}
```

### 세 가지의 관계

NestJS에서 컨트롤러, 서비스, 모듈은 아래와 같이 상호작용합니다:

- 컨트롤러는 요청을 받아서 데이터를 처리할 필요가 있을 때 서비스를 호출합니다.
- 서비스는 요청에 따라 비즈니스 로직을 수행하고, 데이터를 생성/조회/수정합니다.
- 모듈은 이러한 컨트롤러와 서비스를 묶어서 애플리케이션의 기능을 독립적으로 관리할 수 있도록 도와줍니다.