# Australia example

##Basic

	cat input.sgm | python -m chisel.sampler proxy.ini target.ini chisel.ini cdec.ini --scaling 5 --samples 100 --input-format cdec > output.txt

## More

Try playing with n-gram LM features.

These features are implemented in `chisel.ff.klm`. Check it for help:

	python -m chisel.ff.klm
	
1. run

		cat input.sgm | python -m chisel.sampler proxy2.ini target2.ini chisel2.ini cdec2.ini --scaling 5 --samples 100 --input-format cdec -f chisel.ff.klm > output2.txt
		
Understanding this

1. first we need to configure the module, you will notice the difference between `chisel.ini` and `chisel2.ini` is the path to a 3-gram kenlm file.
		
		KLanguageModel=trigram.klm

2. if you checked the help message, you know that the module defines some features, let's focus on two of them, (i.e. `LanguageModel` and `LanguageModel_OOV`). Let's add weights for them in the `target` model (compare `target.ini` and `target2.ini`).

		LanguageModel 1.0
		LanguageModel_OOV 1.0


3. because we complicated the `target` it is useful to start from a better `proxy`, so let's tell `cdec` to use a stateless (read cheaper) version of the n-gram LM feature (compare `cdec.ini` and `cdec2.ini`)
		
		feature_function=StatelessKLanguageModel trigram.klm

		
4. finally we add feature weights to the `proxy` model (compare `proxy.ini` and `proxy2.ini`)

		StatelessLanguageModel 1.0
		StatelessLanguageModel_OOV 1.0
		