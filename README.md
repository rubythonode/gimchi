# Gimchi [![Build Status](https://travis-ci.org/junegunn/gimchi.png?branch=master)](https://travis-ci.org/junegunn/gimchi)

Gimchi is a simple Ruby gem for handling Korean characters.

Features:
- Decompose a Korean character into its 3 components, namely chosung, jungsung and optional jongsung
- Compose elements back into the Korean character
- Read numbers in Korean
- Pronounce Korean characters
- Romanize Korean characters

Gimchi (partially) implements the following rules dictated by
The National Institute of The Korean Language (http://www.korean.go.kr)
- Korean Standard Pronunciation
- Korean Romanization

## Installation

```
gem install gimchi
```

## Usage

### Composing and decomposing Korean character

```ruby
chosung, jungsung, jongsung = Gimchi.decompose "한"

Gimchi.compose chosung, jungsung, jongsung    # 한
Gimchi.compose chosung, "ㅗ", jongsung        # 혼
```

### Inspecting Korean characters
```ruby
Gimchi.korean_char? 'ㄱ'           # true
Gimchi.complete_korean_char? 'ㄱ'  # false

Gimchi.korean_char? 'ㅏ'           # true
Gimchi.complete_korean_char? 'ㅏ'  # false

Gimchi.korean_char? '가'           # true
Gimchi.complete_korean_char? '가'  # true

# Alias of korean_char?
Gimchi.kchar? '가'                 # true

Gimchi.chosung?  'ㄱ'              # true
Gimchi.jungsung? 'ㄱ'              # false
Gimchi.jongsung? 'ㄱ'              # true

Gimchi.chosung?  'ㅏ'              # false
Gimchi.jungsung? 'ㅏ'              # true
Gimchi.jongsung? 'ㅏ'              # false

Gimchi.chosung?  'ㄺ'              # false
Gimchi.jungsung? 'ㄺ'              # false
Gimchi.jongsung? 'ㄺ'              # true
```

### Using Gimchi::Char

```ruby
kc = Gimchi::Char("한")
kc.class                    # Gimchi::Char

kc.chosung                  # "ㅎ"
kc.jungsung                 # "ㅏ"
kc.jongsung                 # "ㄴ"
kc.to_a                     # ["ㅎ", "ㅏ", "ㄴ"]
kc.to_s                     # "한"

kc.complete?                # true
kc.partial?                 # false

Gimchi::Char("ㅏ").partial? # true

# Modifying its elements
kc.chosung = 'ㄷ'
kc.jongsung = 'ㄹ'
kc.to_s                     # "달"
kc.complete?                # true
kc.partial?                 # false

kc.chosung = nil
kc.jongsung = nil
kc.complete?                # false
kc.partial?                 # true
```

### Reading numbers in Korean
```ruby
Gimchi.read_number(1999)         # "천 구백 구십 구"
Gimchi.read_number(- 100.123)    # "마이너스 백점일이삼"
Gimchi.read_number("153,191,100,678.3214")
  	# "천 오백 삼십 일억 구천 백 십만 육백 칠십 팔점삼이일사"

# Age, Time ( -살, -시 )
Gimchi.read_number("20살")       # "스무살"
Gimchi.read_number("13 살")      # "열세 살"
Gimchi.read_number("7시 30분")   # "일곱시 삼십분"
```

### Standard pronunciation (partially implemented)
```ruby
str = "됐어 됐어 이제 그런 가르침은 됐어 매일 아침 7 시 30 분까지 우릴 조그만 교실로 몰아넣고"
Gimchi.pronounce str
  # "돼써 돼써 이제 그런 가르치믄 돼써 매일 아침 일곱 시 삼십 분까지 우릴 조그만 교실로 모라너코"

Gimchi.pronounce str, :slur => true
  # "돼써 돼써 이제 그런 가르치믄 돼써 매이 라치 밀곱 씨 삼십 뿐까지 우릴 조그만 교실로 모라너코"

Gimchi.pronounce str, :number => false
  # "돼써 돼써 이제 그런 가르치믄 돼써 매일 아침 7 시 30 분까지 우릴 조그만 교실로 모라너코"

Gimchi.pronounce str, :each_char => true
  # "됃어 됃어 이제 그런 가르침은 됃어 매일 아침 일곱 시 삼십 분까지 우릴 조그만 교실로 몰아너고"
```

### Romanization (partially implemented)
```ruby
str = "됐어 됐어 이제 그런 가르침은 됐어 매일 아침 7 시 30 분까지 우릴 조그만 교실로 몰아넣고"

Gimchi.romanize str
  # "Dwaesseo dwaesseo ije geureon gareuchimeun dwaesseo mae-il achim ilgop si samsip bunkkaji uril jogeuman gyosillo moraneoko"

Gimchi.romanize str, :slur => true
  # "Dwaesseo dwaesseo ije geureon gareuchimeun dwaesseo mae-i rachi milgop ssi samsip ppunkkaji uril jogeuman gyosillo moraneoko"

Gimchi.romanize str, :number => false
  # "Dwaesseo dwaesseo ije geureon gareuchimeun dwaesseo mae-il achim 7 si 30 bunkkaji uril jogeuman gyosillo moraneoko"

Gimchi.romanize str, :as_pronounced => false
  # "Dwaet-eo dwaet-eo ije geureon gareuchim-eun dwaet-eo mae-il achim ilgop si samsip bunkkaji uril jogeuman gyosillo mol-aneogo"
```

## Limitation of the implementation

Unfortunately in order to implement the complete specification of Korean
pronunciation and romanization, we need NLP, huge Korean dictionaries and even
semantic analysis of the given string. And even with all those complex
processing, we cannot guarantee 100% accuracy of the output. So yes, that is
definitely not what this gem tries to achieve. Gimchi tries to achieve "some"
level of accuracy with relatively simple code.

Currently, Gimchi code contains a lot of ad-hoc (possibly invalid) patches
that try to improve the quality of the output, which should better be
refactored anytime soon.

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Added some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

## Copyright

Copyright (c) 2013 Junegunn Choi. See LICENSE.txt for
further details.

