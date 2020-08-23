```cryptol
module scripts::Test::CorrectQED where

orSelf : Bit -> Bit
property orSelf x = x \/ ~x
```

```Xcryptol session
Loading module Cryptol
Cryptol> :m scripts::Test::CorrectQED
Loading module Cryptol
Loading module scripts::Test::CorrectQED
scripts::Test::CorrectQED> :prove orSelf
Q.E.D.
(Total Elapsed Time: 0.005s, using "Z3")
```
