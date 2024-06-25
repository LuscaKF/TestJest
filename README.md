# Projeto TypeScript com Testes em Jest

Este projeto demonstra como configurar um ambiente de desenvolvimento TypeScript com testes unitários utilizando Jest.

## Estrutura do Projeto

/meu-projeto
|-- /src
| |-- soma.ts
|-- /tests
| |-- soma.test.ts
|-- .gitignore
|-- jest.config.js
|-- package.json
|-- README.md
|-- tsconfig.json
|-- ts-jest-config.js

## Pré-requisitos

- Node.js (>= 14)
- npm (>= 6) ou yarn (>= 1.22)

## Instalação

1. Clone o repositório:

```bash
git clone https://github.com/seu-usuario/meu-projeto.git
cd meu-projeto
Instale as dependências:
npm install
# ou
yarn install

Configuração
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
jest.config.js
Configure o Jest para trabalhar com TypeScript:

module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  testMatch: ['**/tests/**/*.test.ts'],
  moduleFileExtensions: ['ts', 'js'],
};

Exemplo de Código
src/soma.ts
typescript
Copiar código
export function soma(a: number, b: number): number {
  return a + b;
}

tests/soma.test.ts
typescript
Copiar código
import { soma } from '../src/soma';

test('soma 1 + 2 para ser igual a 3', () => {
  expect(soma(1, 2)).toBe(3);
});

Executando os Testes
Para executar os testes, utilize o comando:

npm test
# ou
yarn test
Comandos Úteis
npm run build ou yarn build: Compila o código TypeScript para JavaScript.
npm run test ou yarn test: Executa os testes com Jest.
Contribuição
Contribuições são bem-vindas! Sinta-se à vontade para abrir uma issue ou enviar um pull request.

Licença
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
Com estas instruções, você deve estar apto a configurar e rodar um ambiente de desenvolvimento TypeScript com testes unitários utilizando Jest.
