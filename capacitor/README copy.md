Per a fer que el projecte generi una build de android haurem d'instalar ionic capacitor

```bash
npm install @capacitor/core @capacitor/cli
```

Després farem una build de Vue

```bash
npm run build
```

I despres iniciarem el projecte de capacitor amb

```bash
npx cap init --web-dir=dist
```

Després instalarem els paquets per fer la build a android o ios i afegirem els que volguem

```bash
npm i @capacitor/ios @capacitor/android
npx cap add android
npx cap add ios
```

i després farem una build de capacitor amb 

```bash
npx cap copy
npx cap sync
```