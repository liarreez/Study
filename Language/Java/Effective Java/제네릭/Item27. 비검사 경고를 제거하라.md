# 비검사 경고를 제거하라

<br>

> 비검사 경고는런타임에 ClassCastException을 일으킬 수 있으니 가능한 제거하라

<br>

## @SuppressWarning("unchecked")

---

> 경고를 제거할 수 없지만 타입 안전하다고 확신할 때 @SuppressWarning("unchecked") 애너테이션으로 경고를 숨김

- 애너테이션은 선언에만 달 수 있으므로 return문에 사용 불가
- 비검사 경고를 숨기면 다른 경고도 모두 묻힐 수 있기에 확실히 증명해야 함
- @SuppressWarning 애너테이션은 항상 가능한 좁은 범위에 적용
- @SuppressWarning 애너테이션을 사용할 때는 경고를 무시해도 되는 이유를 주석으로 남겨야 함
