# 기술 블로그

> 아래 내용은 **'velog 포스팅 시, 깃허브에 자동 커밋시키기'** 를 진행하면서 겪은 트러블 슈팅입니다.  
### velog to Github : Github Actions 트러블 슈팅 
#### 1. remote: Write access to repository not granted : 403 Error 발생
![스크린샷 2024-10-22 02 39 30](https://github.com/user-attachments/assets/f30bcc06-d2b5-4c48-8fc7-61c8ce83b820)


모든 사용자에게 권한을 주는 방법도 존재하지만, 최소한의 권한 수정으로 문제를 해결하는 것이 보안 측면에서 이롭다.
따라서 `update_blog.yml` 에 아래 설정을 추가해준다.
```yaml
# update_blog.yml - jobs: 부분에 설정 추가
jobs:
  job-name:
    permissions:
      contents: write
```

다시 push 후 Actions 탭을 확인해보면, 아래와 같이 해결된 것을 볼 수 있다.

![스크린샷 2024-10-22 02 40 46](https://github.com/user-attachments/assets/3ad4da12-5204-4ae4-949a-3b3f3de8eba3)


### 참고 링크
- https://stackoverflow.com/questions/72851548/permission-denied-to-github-actionsbot

---
