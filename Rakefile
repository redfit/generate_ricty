@conf = {
  inconsolata: 'http://levien.com/type/myfonts/Inconsolata.otf',
  migu1m: 'http://dl.sourceforge.jp/mix-mplus-ipa/59022/migu-1m-20130617.zip',
  powerline: {
    patcher: 'https://raw.github.com/Lokaltog/powerline/develop/font/fontpatcher.py',
    symbol: 'https://raw.github.com/Lokaltog/powerline/develop/font/fontpatcher-symbols.sfd',
  }
}
@rictydir = File.expand_path 'Ricty'
@fontdir = File.expand_path 'font'
@productsdir = File.expand_path 'products'
@windowsdir = File.expand_path(File.join(@productsdir, 'windows'))

def download uri, workdir = '.'
  archive = File.split(uri).last

  chdir workdir do
    return if File.exists? archive

    sh "curl -kLO# #{uri}"
    yield uri, archive if block_given?
  end
end

def ricty_script file, opt = {}
  subdir = opt[:misc] ? 'misc' : ''
  File.join @rictydir, subdir, file
end

def sanitize_fontname fontfile
  File.rename fontfile, fontfile.gsub(/\s/, '-').sub(/(?<=Ricty)-(?=Discord)/, '')
end

def sanitize_fontnames fontfiles
  fontfiles.each{|f| sanitize_fontname f}
end

task :default => [:initialize, "ricty:default", "powerline:default", "ricty:windows", "powerline:windows"]

task :initialize do
  sh "git submodule update --init"
end

namespace :download do
  task :createdir do
    unless Dir.exists? @fontdir
      mkdir @fontdir
    end
  end

  desc 'download fonts'
  task :download => [:inconsolata, :migu1m]

  desc 'download Inconsolata'
  task :inconsolata => :createdir do |t|
    download @conf[:inconsolata], @fontdir
  end

  desc 'download Migu 1M'
  task :migu1m => :createdir do
    download @conf[:migu1m], @fontdir do |uri, archive|
      sh "unzip", "-j", archive, "*/*.ttf"
      #rm archive
    end
  end
end

namespace :ricty do
  task :default => [:generate, :ascii]

  task :fix_script do
    f = File.open(ricty_script('ricty_discord_patch.pe'), 'r')
    s = f.read

    s.gsub! /^(flag_(\w+))="true"/ do |s|
      $2 == 'l' ? s : %{#{$1}="false"}
    end

    f.reopen(f.path, 'w').write(s)
    f.close
  end

  task :createdir do
    unless Dir.exists? @productsdir
      mkdir @productsdir
    end

    unless Dir.exists? @windowsdir
      mkdir @windowsdir
    end
  end

  desc 'generate ricty font'
  task :generate => [:fix_script, 'download:download', :createdir] do
    fonts = FileList.new do |f|
      patterns = ['Inconsolata.otf', '*-regular.ttf', '*-bold.ttf'].map {|s| File.join(@fontdir, s) }
      f.include *patterns
    end

    chdir @productsdir do
      sh "sh", ricty_script('ricty_generator.sh'), "-z", *fonts
    end
  end

  task :ascii => :generate do
    chdir @productsdir do
      fonts = FileList.new('RictyDiscord-*.ttf') do |f|
        f.exclude /-Powerline\.ttf/
      end
      sh "fontforge", "-lang=py -script", ricty_script('ricty_ascii_extractor.pe', :misc => true), *fonts
    end
  end

  task :windows => :generate do
    chdir @productsdir do
      fonts = FileList['*.ttf']
      sh ricty_script('os2version_reviser.sh', :misc => true), *fonts
      mv fonts, @windowsdir
      Dir['*.bak'].each do |f|
        mv f, f.sub(/\.bak/, '')
      end
    end
  end
end

namespace :powerline do
  task :default => [:generate]

  task :download do
    download @conf[:powerline][:patcher] do |uri, archive|
      chmod 0755, archive
    end
    download @conf[:powerline][:symbol]
  end

  desc 'powerline'
  task :generate => :download do
    chdir @productsdir do
      fonts = FileList['RictyDiscord-*.ttf']
      sh "../fontpatcher.py", *fonts

      sanitize_fontnames FileList['*for Powerline.ttf']
    end
  end

  desc 'powerline for win'
  task :windows => :generate do
    chdir @windowsdir do
      rm FileList['RictyDiscord-*Powerline.ttf']
      fonts = FileList['RictyDiscord-*.ttf']
      sh "../../fontpatcher.py", *fonts

      sanitize_fontnames FileList['*for Powerline.ttf']
    end
  end
end
