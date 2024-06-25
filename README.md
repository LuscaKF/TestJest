# Projeto TypeScript com Testes em Jest

Este projeto demonstra como configurar um ambiente de desenvolvimento TypeScript com testes unitários utilizando Jest.

## Estrutura do Projeto

/meu-projeto
|-- /src
| |-- /classes
| | |-- /interfaces
| | | |-- cart-item.ts
| | | |-- customer-protocol.ts
| | | |-- messaging-protocol.ts
| | | |-- order-status.ts
| | | |-- persistency-protocol.ts
| | | |-- shopping-cart-protocol.ts
| | |-- customer.ts
| | |-- discount.ts
| | |-- order.ts
| | |-- product.ts
| | |-- shopping-cart.ts
|-- /tests
| |-- order.test.ts
| |-- shopping-cart.test.ts
|-- .gitignore
|-- jest.config.js
|-- package.json
|-- README.md
|-- tsconfig.json

## Pré-requisitos

- Node.js (>= 14)
- npm (>= 6) ou yarn (>= 1.22)

## Instalação

1. Clone o repositório:

```bash

git clone https://github.com/seu-usuario/meu-projeto.git
cd meu-projeto

## Instale as dependências:

npm install
# ou
yarn install

# Configuração
tsconfig.json

Certifique-se de que seu tsconfig.json está configurado corretamente:

{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true
  }
}

# jest.config.js
Configure o Jest para trabalhar com TypeScript:

module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  testMatch: ['**/tests/**/*.test.ts'],
  moduleFileExtensions: ['ts', 'js'],
};

## Exemplo de Código
src/classes/interfaces/cart-item.ts

export interface CartItem {
  name: string;
  price: number;
}

# src/classes/interfaces/customer-protocol.ts

export interface Customer {
  getName(): string;
  getIDN(): string;
}

# src/classes/interfaces/messaging-protocol.ts

export interface Messaging {
  sendMessage(msg: string): void;
}

# src/classes/interfaces/order-status.ts

export type OrderStatus = 'open' | 'closed';

# src/classes/interfaces/persistency-protocol.ts

export interface Persistency {
  saveOrder(): void;
}

# src/classes/interfaces/shopping-cart-protocol.ts

import { CartItem } from './cart-item';

export interface ShoppingCartProtocol {
  addItem(item: CartItem): void;
  removeItem(index: number): void;
  getTotal(): number;
  isEmpty(): boolean;
  clear(): void;
}

# src/classes/customer.ts

import { Customer } from './interfaces/customer-protocol';

export class IndividualCustomer implements Customer {
  constructor(
    public firstName: string,
    public lastName: string,
    public cpf: string,
  ) {}

  getName(): string {
    return this.firstName + ' ' + this.lastName;
  }

  getIDN(): string {
    return this.cpf;
  }
}

# src/classes/discount.ts

export abstract class Discount {
  protected discount = 0;

  calculate(price: number): number {
    return price - price * this.discount;
  }
}

export class NoDiscount extends Discount {}

export class TenPercentDiscount extends Discount {
  protected readonly discount = 0.1;
}

# src/classes/order.ts

import { OrderStatus } from './interfaces/order-status';
import { ShoppingCart } from './shopping-cart';
import { Messaging } from './interfaces/messaging-protocol';
import { Persistency } from './interfaces/persistency-protocol';

export class Order {
  private _orderStatus: OrderStatus = 'open';

  constructor(
    private readonly cart: ShoppingCart,
    private readonly messaging: Messaging,
    private readonly persistency: Persistency,
  ) {}

  get orderStatus(): OrderStatus {
    return this._orderStatus;
  }

  checkout(): void {
    if (this.cart.isEmpty()) {
      console.log('Seu carrinho está vazio');
      return;
    }

    this._orderStatus = 'closed';
    this.messaging.sendMessage(
      `Seu pedido com total de ${this.cart.getTotal()} foi recebido.`,
    );
    this.persistency.saveOrder();
    this.cart.clear();
  }
}

# src/classes/product.ts

import { CartItem } from './interfaces/cart-item';

export class Product implements CartItem {
  constructor(public name: string, public price: number) {}
}

# src/classes/shopping-cart.ts

import { CartItem } from './interfaces/cart-item';
import { ShoppingCartProtocol } from './interfaces/shopping-cart-protocol';

export class ShoppingCart implements ShoppingCartProtocol {
  private readonly _items: CartItem[] = [];

  addItem(item: CartItem): void {
    this._items.push(item);
  }

  removeItem(index: number): void {
    this._items.splice(index, 1);
  }

  getTotal(): number {
    return +this._items
      .reduce((total, next) => total + next.price, 0)
      .toFixed(2);
  }

  isEmpty(): boolean {
    return this._items.length === 0;
  }

  clear(): void {
    this._items.length = 0;
  }
}

## Exemplo de Testes
# tests/order.test.ts

import { Order } from '../src/classes/order';
import { ShoppingCart } from '../src/classes/shopping-cart';

describe('Order', () => {
  it('should be able to place an order', () => {
    const shoppingCartMock = {
      isEmpty: jest.fn().mockReturnValue(false),
      getTotal: jest.fn().mockReturnValue(50),
      clear: jest.fn(),
    } as unknown as ShoppingCart;
    const messagingMock = {
      sendMessage: jest.fn(),
    };
    const persistencyMock = {
      saveOrder: jest.fn(),
    };
    const order = new Order(shoppingCartMock, messagingMock, persistencyMock);

    order.checkout();

    expect(shoppingCartMock.clear).toHaveBeenCalled();
    expect(messagingMock.sendMessage).toHaveBeenCalledWith(
      'Seu pedido com total de 50 foi recebido.',
    );
    expect(persistencyMock.saveOrder).toHaveBeenCalled();
  });
});

# tests/shopping-cart.test.ts

import { ShoppingCart } from '../src/classes/shopping-cart';
import { Product } from '../src/classes/product';

describe('ShoppingCart', () => {
  it('should add items to the cart', () => {
    const cart = new ShoppingCart();
    const product = new Product('Test Product', 10);

    cart.addItem(product);

    expect(cart.getTotal()).toBe(10);
    expect(cart.isEmpty()).toBe(false);
  });

  it('should remove items from the cart', () => {
    const cart = new ShoppingCart();
    const product = new Product('Test Product', 10);

    cart.addItem(product);
    cart.removeItem(0);

    expect(cart.getTotal()).toBe(0);
    expect(cart.isEmpty()).toBe(true);
  });

  it('should clear the cart', () => {
    const cart = new ShoppingCart();
    const product = new Product('Test Product', 10);

    cart.addItem(product);
    cart.clear();

    expect(cart.getTotal()).toBe(0);
    expect(cart.isEmpty()).toBe(true);
  });
});

# Executando os Testes
Para executar os testes, utilize o comando:

npm test
# ou
yarn test

# Comandos Úteis
npm run build ou yarn build: Compila o código TypeScript para JavaScript.
npm run test ou yarn test: Executa os testes com Jest.
```

# Contribuição
Contribuições são bem-vindas! Sinta-se à vontade para abrir uma issue ou enviar um pull request.

## Licença
Este projeto está licenciado sob a licença MIT. Veja o arquivo LICENSE para mais detalhes.

### Passos Adicionais

1. **Instalação do Jest e ts-jest**

   Certifique-se de que você tenha Jest e ts-jest instalados como dependências de desenvolvimento:

   ```bash
   npm install --save-dev jest ts-jest @types/jest
   # ou
   yarn add --dev jest ts-jest @types/jest
   Scripts no package.json

   Adicione os scripts necessários no seu package.json:

   {
     "scripts": {
     "build": "tsc",
     "test": "jest"
     }
   }
   ```


## Com estas instruções, você deve estar apto a configurar e rodar um ambiente de desenvolvimento TypeScript com testes unitários utilizando Jest.
