# Australia example

##Basic

    cat input.sgm | python -m chisel.sampler config.ini > basic.out

## More

Try playing with n-gram LM features.

These features are implemented in `chisel.ff.klm`. Check it for help:

    python -m chisel.ff.klm
    
### Using `chise.ff.klm`

        cat input.sgm | python -m chisel.sampler config2.ini > trigram.out

Understanding this (compare `config.ini` and `config2.ini`)

1. first we need to include the module

        [chisel:scorers]
        klm='chisel.ff.klm'

2. this scorer requires a trained LM, thus we need to configure the scorer


        [chisel:scorers:config]
        klm.model='trigram.klm'

3. if you checked the help message, you know that the module defines some features, let's focus on two of them, (i.e. `LanguageModel` and `LanguageModel_OOV`). Let's add weights for them in the `target` model 

        [target]
        # ...
        LanguageModel=1.0
        LanguageModel_OOV=1.0

4. because we complicated the `target` it is useful to start from a better `proxy`, so let's tell `cdec` to use a `stateless` (read `cheaper`) version of the n-gram LM feature 

        [cdec:features]
        # ...
        StatelessKLanguageModel=trigram.klm
        # this feature is implicitly added to cdec.ini as: 
        # feature_function=StatelessKLanguageModel trigram.klm

5. finally we add feature weights to the `proxy` model (compare `proxy.ini` and `proxy2.ini`)

        [proxy]
        # ...
        StatelessLanguageModel 1.0
        StatelessLanguageModel_OOV 1.0

