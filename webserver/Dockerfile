# nginx:alpine을 베이스 이미지로 사용 (가볍고 빠른 웹서버)
FROM nginx:alpine
# 이미지에 이름표(메타데이터) 붙이기
LABEL org.opencontainers.image.title="my-custom-nginx"
# 개발 환경임을 나타내는 환경변수 설정
ENV APP_ENV=dev
# 내가 만든 site 폴더의 정적 페이지로 nginx 기본 페이지를 교체
COPY site/ /usr/share/nginx/html/
