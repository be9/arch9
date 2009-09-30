ARCH = `arch`

def package(pkg)
  pkgname = pkg.is_a?(Hash) ? pkg.keys.first : pkg
  pkgdir = pkgname.to_s.gsub('_','-')

  buildok = "#{pkgdir}/.build_ok"

  desc "Build #{pkgdir} package"
  task(pkg)
  task(pkgname => buildok)
  task :packages => pkgname

  file buildok => ["#{pkgdir}/PKGBUILD"] do
    sh "cd #{pkgdir} && rm -rf pkg src && makepkg -f && cp *.pkg.tar.gz ../repo/#{ARCH}/"

    FileUtils.touch buildok
  end

  task :all_packages => pkgname
end

###################################

namespace :clean do
  desc 'Clean package files'
  task :packages do
    FileUtils.rm_f FileList['*/**.pkg.tar.gz']+FileList['*/.build_ok']
  end
end

desc 'Build all packages'
task :all_packages

task :default => :all_packages

###################################

package :mysql_ruby_enterprise => :ruby_enterprise
package :ruby_enterprise
