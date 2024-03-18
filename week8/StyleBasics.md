## 목표
### - Style Basics

- Basic: Class
- Basic: Inline Style

---

## 내용
### 1. Basic: Class

```html
<style>
  .greeting {
    color: #00F;
  }
</style>
```
- index.html 파일에 간단히 CSS 추가.

```tsx
export default function Greeting() {
    return (
        <p className="greeting">
            Hello, world!
        </p>
    );
} 
```

- “className” 지정.
- CSS는 컴포넌트를 전제로 하고 있지 않음. 
- 공통된 부분이 많을 때 재사용하기 쉽다.
- 따라서, 컴포넌트 중심으로 빠르게 작업하려고 하면 불편할 때가 많다.
- 재사용은 그냥 컴포넌트를 사용하면 된다.
- 절충안으로 아주 작은 스타일 그룹을 클래스로 지정하는 방법도 있긴 하다(Bootstrap, Tailwind CSS 등의 접근법).


### 2. Basic: Inline Style

- “style” 속성 활용.
- 평범한 JavaScript 객체를 활용하므로 변수, 함수 등을 재사용하기 쉽다.
- 텍스트가 아니라서 실수하거나 불편할 때가 있다.
- TypeScript의 힘으로 자동완성, 타입 검사 등의 도움을 받을 수 있다.

```tsx
export default function Greeting() {
  const style = {
    color: '#00F',
  };

  return (
    <p style={style}>
      Hello, world!
    </p>
  );
}
```
```tsx
export default function Greeting() {
  return (
    <p
      style={{
        color: '#00F',
      }}
    >
      Hello, world!
    </p>
  );
}
```
