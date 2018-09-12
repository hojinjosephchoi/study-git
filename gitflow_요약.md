# gitflow

- [원문](https://nvie.com/posts/a-successful-git-branching-model/)
- [번역](http://dogfeet.github.io/articles/2011/a-successful-git-branching-model.html)
- [추가설명](http://amazingguni.github.io/blog/2016/03/git-branch-%EA%B7%9C%EC%B9%99)


![gitflow image](http://dogfeet.github.io/articles/2011/a-successful-git-branching-model/git-branching-model.png)


## 주요 브랜치
![main branches](http://dogfeet.github.io/articles/2011/a-successful-git-branching-model/main-branches.png)

- master : 먼저 배포했거나, 곧 배포할 (production-ready) 코드는 origin/master에 두고 관리
- develop : 다음에 배포할 것을 개발하는 코드는 origin/develop에 두고 관리
- origin/develop 브랜치는 '통합 브랜치(integration branch)'라고 부르기도 하며, 이 브랜치를 자동으로 매일 빌드하는데 사용한다.
- develop 브랜치의 코드가 안정되고 배포할 준비가 되면 곧 master로 merge하고 배포 버전으로 tag를 단다.
- master로 merge하는 것은 새 버전을 배포하는 것을 의미.
- master브랜치에 커밋될 때 마다 git hook 스크립트로 자동으로 빌드하고 운영서버로 배포할 수 있다.

## 보조 브랜치
- 기능을 구현하고, 배포를 준비하고, 이미 배포한 제품이나 서비스의 버그를 빠르게 해결하는 브랜치
- feature
- release
- hotfix

### feature
~~~
갈라져 나온 브랜치: develop
다시 merge할 브랜치: develop
브랜치 이름 규칙: master, develop, release-*, hotfix-*를 제외한 것
~~~
- feature 브랜치 (또는 토픽 브랜치) : 기능을 개발하는 브랜치
- 기능이 다 완성되면 develop브랜치로 merge, 또는 아예 버리기도 한다.
- 보통 **개발자 저장소** 에만 있는 브랜치고 origin에는 push하지 않는다.

#### feature 브랜치 생성
~~~
$ git checkout -b myfeature develop
~~~

#### feature 브랜치 마치기 (feature 개발완료 상황)
~~~
$ git checkout develop
$ git merge --no-ff myfeature
$ git branch -d myfeature
$ git push origin develop
~~~
- `--no-ff` 옵션을 주고 merge하면 fast-forward로 merge할 수 있어도 fast-forward하지 않는다. 그러면 feature 브랜치에 추가된 모든 커밋이 merge되고 feature 브랜치에서 merge 했다는 기록이 커밋 히스토리에 남는다.
![--no-ff 옵션 사용](http://dogfeet.github.io/articles/2011/a-successful-git-branching-model/merge-without-ff.png)
- 나중에 커밋 히스토리를 다시 확인할 때 어떤 기능(feature)을 구현한 것인지 확인할 때, 해당 feature와 관련된 모든 커밋을 되돌려야 할 때(revert) 유용하다.

### release
~~~
갈라져 나온 브랜치: develop
다시 merge할 브랜치: develop, master
브랜치 이름 규칙: release-*
~~~
- release 브랜치 : 제품 배포를 준비하는 브랜치
- 배포하는데 필요한 버전 넘버, 빌드 일정 등의 메타데이터를 준비하고 사소한 버그를 잡는다.
- 배포해야 하는 기능(feature)이 모두 develop 브랜치에 merge되어 있어야 한다.
- 이번에 배포하지 않을 기능은 release 브랜치를 만들 때까지 기다려야 한다.

#### release 브랜치 생성
~~~
$ git checkout -b release-1.2 develop
$ ./bump-version.sh 1.2 (버전넘버가 들어가있는 파일 전부 수정하는 가상 쉘 스크립트)
$ git commit -a -m "Bumped version number to 1.2"
~~~
- 그동안 발견한 버그는 develop 브랜치가 아닌 release-* 브랜치 내에서 해결
- 새 기능은 이 브랜치에 추가하지 않는다.

#### release 브랜치 마치기 (배포 준비 완료 상황)
~~~
$ git checkout master
$ git merge --no-ff release-1.2
$ git tag -a 1.2

$ git checkout develop
$ git merge --no-ff release-1.2

$ git branch -d release-1.2
~~~
- 배포할 상태가 되면 master에 배포한다.
- 해당 버전을 찾기 쉽게 태그를 만들어 master에 merge된 커밋에 등록한다.
- develop 브랜치에도 merge하여 release 브랜치에서 버그수정된 것이 반영되도록 한다.
- 이 때 버전 넘버를 수정했으므로 여기서 merge conflict 날 확률이 높다.
- 마지막으로 release 브랜치를 삭제한다.

### hotfix
~~~
갈라져 나온 브랜치: master
다시 merge할 브랜치: develop, master
브랜치 이름 규칙: hotfix-*
~~~
- hotfix 브랜치 : 이미 배포한 운영버전에 문제 발생 시 해결을 위한 브랜치

![hotfix gitflow](http://dogfeet.github.io/articles/2011/a-successful-git-branching-model/hotfix-branches.png)

#### hotfix 브랜치 생성
~~~
$ git checkout -b hotfix-1.2.1 master
$ ./bump-version.sh 1.2.1
$ git commit -a -m "Bumped version number to 1.2.1"

... 버그 수정 ...

$ git commit -m "Fixed severe production problem"
~~~

#### hotfix 브랜치 마치기 (hotfix 수정 후 배포)
~~~
$ git checkout master
$ git merge --no-ff hotfix-1.2.1
$ git tag -a 1.2.1

$ git checkout develop
$ git merge --no-ff hotfix-1.2.1

$ git branch -d hotfix-1.2.1
~~~
- 배포할 상태가 되면 master에 배포하며, release와 동일하게 태그도 추가한다.
- develop 브랜치에도 merge하여 hotfix 브랜치에서 버그수정된 것이 반영되도록 한다.
- 만약 release 브랜치가 삭제되지 않고 있다면 release 브랜치에 merge한다. (release 브랜치가 완료될 때 develop에 merge할 것이기 때문에...)
- 만약 develop 브랜치에도 즉시 해결해야 하면 release 브랜치가 끝나길 기다리지 말고, develop 브랜치에 즉시 그리고 조심스럽게 merge 한다.

