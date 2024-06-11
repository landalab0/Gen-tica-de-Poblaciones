## Como instalar programas dentrode un ambiente conda

**1. Abra la terminal en su sistema operativo favorito (preferentemente uno que sea base linux)**

**2. Instale conda**

**3. Antes de comenzar, ubique el programa que desea instalar**

**4. Teclee lo siguite** 
*      usuario$ conda create --name env-name --yes
*      usuario$ conda install -c bioconda programa -n env-name --yes
*      usuario$ conda activate env-name (se activará nuestro ambiente 

**5. Para saber si el programa se instaló correctamente teclee el nombre del programa y solicite el manual**
*    	usuario$ man programa; programa --help, programa -h, etc
6. Para desactivar un ambiente y por ende un programa teclee
*   	usuario$ conda deactivate
7. Para saber cuantos ambientes ha creado usted puede teclear 
*	    usuario$ conda list


Adjunto el breve manual de conda:

usage: conda-env [-h] {create,export,list,remove,update,config} ...

positional arguments:
  {create,export,list,remove,update,config}
    create              Create an environment based on an environment definition file. If using an environment.yml file (the default),
                        you can name the environment in the first line of the file with 'name: envname' or you can specify the
                        environment name in the CLI command using the -n/--name argument. The name specified in the CLI will override the
                        name specified in the environment.yml file. Unless you are in the directory containing the environment definition
                        file, use -f to specify the file path of the environment definition file you want to use.
    export              Export a given environment
    list                List the Conda environments
    remove              Remove an environment
    update              Update the current environment based on environment file
    config              Configure a conda environment

optional arguments:
  -h, --help            Show this help message and exit.

conda commands available from other packages:
*     content-trust
*     env





1463014.275
