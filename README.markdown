## Otcopress blog source of www.pudgecon.com

### Development

```
git clone git@github.com:pudgecon/pudgecon.github.com.git --branch source --single-branch
cd pudgecon.github.com
bundle
rake preview
```

### Deploy

```
cd pudgecon.github.com
git clone git@github.com:pudgecon/pudgecon.github.com.git --branch master --single-branch _deploy
bundle exec rake generate
bundle exec rake deploy
```
