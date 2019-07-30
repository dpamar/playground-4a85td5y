# The trick

Let's suppose our BF interpreter is named `bf`, like the one available in almost all distributions' packages.

Instead of doing `bf myscript.bf`, let's do things differently

* First, locate your bf. `which bf`.
  * Let's say it's /usr/local/bin/bf 
* Then, add a first line at myscript.bf : #!_bf path_
  * e.g. #!/usr/local/bin/bf
* Finally, change permissions on your script. `chmod 755 myscript.bf`

Now, you can execute ./myscript.bf and launch it.

# Advantages

This is really useful, for multiple reasons
* First of all, look at line #!/usr/local/bin/bf
  * Unless you put `-` in the path, there is no BF instruction in there
  * This means the script is both executable, and interpretable as is : first line only contains BF comments, that's all
* No need to compile the code
* And on some BF implementations, the carriage return is not considered as End Of Input by default
  * `bf myscript.bf` that expects an input will read a single line
  * `./myscript.bf` will read carriage returns as `code 10` char, that's all (and Ctrl-C will "close" the program).

Let's implement an example that leverage this new capability.
