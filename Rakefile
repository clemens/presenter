#!/usr/bin/env ruby

# inspired by http://onestepback.org/index.cgi/General/PresentingCode.red

require 'fileutils'
require 'rake/clean'
require 'syntax/convertors/html'

CLOBBER.include("#{ENV['P']}/html")

task :default => :extract

desc "Extract all files from a given presentation's source directory. Use P=your-presentation to specify the presentation to be extracted."
task :extract do
  source_files = Dir[File.join(presentation_base_dir, 'source', '*/*.*')]
  source_files.each do |source_file|
    extract(source_file, source_file.gsub('/source/', '/html/') + '.html')
  end
end

def extract(source_file, outfile)
  language = detect_language_from_extension(source_file) || 'ruby'
  code = open(source_file) { |f| f.read }
  convertor = Syntax::Convertors::HTML.for_syntax(language)
  html = convertor.convert(code)

  FileUtils.mkdir_p(File.dirname(outfile))
  open(outfile, 'w+') do |out|
    out.puts '<html>'
    out.puts '  <head>'
    out.puts %(    <link rel="Stylesheet" type="text/css" href="/#{presentation}/css/base.css" />)
    out.puts %(    <link rel="Stylesheet" type="text/css" href="/#{presentation}/css/#{language}.css" />)
    out.puts '  </head>'
    out.puts %(  <body id="#{generate_body_id(source_file)}" class="#{generate_body_classes(source_file)}">)
    out.puts %(    <pre class="#{language}">)
    out.puts html
    out.puts '    </pre>'
    out.puts '  </body>'
    out.puts '</html>'
  end
end

def presentation
  ENV['P']
end

def presentation_base_dir
  File.join(File.dirname(__FILE__), 'presentations', presentation.to_s)
end

def detect_language_from_extension(source_file)
  extension = File.extname(source_file)
  case extension
  when '.yml' then 'yaml'
  when '.xml' then 'xml'
  else 'ruby'
  end
end

def generate_body_id(source_file)
  relative_path_without_leading_slash(source_file).gsub(/[\/\.]/, '_')
end

def generate_body_classes(source_file)
  relative_path_without_leading_slash(source_file).split(/[\/]/)[0..-2].join(' ')
end

def relative_path_without_leading_slash(source_file)
  source_file.gsub(File.join(presentation_base_dir, 'source', '/'), '')
end
