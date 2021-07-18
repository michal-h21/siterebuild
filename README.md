# siterebuild

Find LaTeX files that needs to be recompiled in projects that use make4ht `staticsite` extensions. 
This is useful in automated environments, like CI, as it avoids the need to recompile all 
source files.


## Usage

Say that you have the following blog structure:

    blog/
    ├── texposts_root/
        ├── first_post/
            ├── first_post.tex
            ├── first_post.published
        ├── second_post/
            ├── second_post.tex
            ├── second_post.published
    ├── html_root/
        ├── 2021-07-18-first-post.html
        ├── 2021-07-19-second-post.html
    ├── .make4ht
    
The `<basename>.published` file is created automatically by the `staticsite` extension when the TeX file is converted for 
the first time. If you rely entirely on the server side compilation, you can create this file using:

    $ date +%s > first_post.published

The `.make4ht` configuration file should contain atleast a minimal configuration for the `staticsite` filter:

    local outdir = "../../html_root"
    
    filter_settings "staticsite" {
      site_root = outdir,
    }
    
    if mode=="publish" then
      Make:enable_extension "staticsite"
      Make:htlatex {}
    end
    


The `outdir` variable can be either full path to the output directory (`html_root`), or path relative to the TeX 
files directory. The relative path is used in the sample configuration file.  

To find files that were modified since the publication time, go to the `textposts_root` directory and execute this script:

    $ cd blog/texposts_root
    $ siterebuild
    

## Options

    -l,--loglevel  (default warning) Messages log level
    -n,--no_config                   Don't load .make4ht config file   
    -p,--file_pattern  (default nil) Pattern used to generate HTML filenames
    -s,--site_root     (default nil) Generated HTML files root
    <tex_root>         (default .)   TeX files root
