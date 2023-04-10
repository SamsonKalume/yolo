FROM node:13.12.0-alpine 

WORKDIR /app

ENV CI=true

COPY package*.json ./

RUN npm install

COPY .  .


#RUN npm run build

EXPOSE 3000

CMD ["npm","run", "start"]