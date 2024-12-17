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

## DTO (Data Transfer Object)

DTO는 Data Transfer Object의 약자로, 데이터를 전송하기 위한 객체를 의미해요. 주로 애플리케이션의 계층 간 데이터 교환을 목적으로 사용됩니다. 예를 들어 클라이언트에서 서버로 데이터를 전달하거나, 서버에서 데이터를 반환할 때 사용해요.

### DTO의 목적

1. 데이터 구조화: 데이터를 객체로 만들어 더 명확하게 전달할 수 있어요.
2. 불필요한 데이터 노출 방지: 필요 없는 정보를 제외하고 필요한 데이터만 전달해요.
3. 데이터 유효성 검사: DTO를 통해 전송되는 데이터의 형태를 검증할 수 있어요.
4. 계층 간 독립성 유지: 데이터베이스 모델(Entity)과 비즈니스 로직을 클라이언트와 분리해줍니다.

### NestJS에서의 DTO

NestJS에서는 클라이언트 요청이나 응답 데이터를 다룰 때 DTO를 자주 사용해요. 특히 요청 데이터를 유효성 검사하기 위해 class-validator와 같은 라이브러리와 함께 사용됩니다.

### DTO의 예시

1. DTO 클래스 정의
```ts
import { IsString, IsInt, MinLength, IsOptional } from 'class-validator';

export class CreateUserDto {
  @IsString()
  @MinLength(2)
  name: string; // 이름 필수, 문자열이며 최소 길이 2

  @IsInt()
  age: number; // 나이 필수, 정수

  @IsString()
  @IsOptional() // 선택적 필드
  address?: string; // 주소 선택사항
}
```

2. 컨트롤러에서 DTO 사용

```ts
import { Body, Controller, Post } from '@nestjs/common';
import { CreateUserDto } from './create-user.dto';

@Controller('users')
export class UserController {
  @Post()
  createUser(@Body() createUserDto: CreateUserDto) {
    return {
      message: 'User created successfully',
      data: createUserDto,
    };
  }
}
```

- `@Body()` 데코레이터를 통해 클라이언트의 요청 데이터를 `CreateUserDto`로 전달받아요.
- NestJS는 데이터의 형태를 자동으로 검증하고, 오류가 발생하면 적절한 에러를 반환합니다.

3. 유효성 검사 라이브러리 사용

class-validator와 class-transformer 라이브러리를 설치해야 DTO 유효성 검사를 할 수 있습니다.

```shell
npm install class-validator class-transformer
```

```ts
// main.ts
import { ValidationPipe } from '@nestjs/common';
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalPipes(new ValidationPipe()); // 글로벌 유효성 검사 설정
  await app.listen(3000);
}
bootstrap();
```

### DTO 사용 시 이점

1. 코드의 가독성: 데이터 구조가 명확해집니다.
2. 유효성 검사: 클라이언트로부터 전송된 데이터를 쉽게 검증할 수 있습니다.
3. 보안 강화: 필요 없는 데이터의 노출을 막고, 원하는 데이터만 전송받거나 반환합니다.
4. 유지보수 용이: 데이터 형식이 변경되면 DTO만 수정하면 되므로 계층 간 영향을 최소화합니다.

### DTO와 Entity의 차이

- DTO는 데이터를 전달하는 용도로 사용되며, 비즈니스 로직이나 데이터베이스와는 관계가 없어요.
- Entity는 데이터베이스 테이블과 직접적으로 매핑되는 객체로, 데이터의 영속성에 사용됩니다.

### 요약
- DTO는 계층 간 데이터를 전송하기 위해 사용하는 객체입니다.
- 데이터 유효성 검사, 불필요한 데이터 제거, 명확한 데이터 구조화에 도움을 줍니다.
- NestJS에서는 class-validator와 함께 DTO를 사용해 요청 데이터를 쉽게 검증할 수 있어요.