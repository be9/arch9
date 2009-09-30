ARCH = `arch`

def package(pkg)
  pkgname = pkg.is_a?(Hash) ? pkg.keys.first : pkg
  pkgdir = pkgname.to_s.gsub('_','-')

  buildok = "#{pkgdir}/.build_ok"
  pkgbuild = "#{pkgdir}/PKGBUILD"

  desc "Build and install #{pkgdir} package"
  task(pkg)
  task(pkgname => buildok)
  task :packages => pkgname

  file buildok => [pkgbuild] do
    Dir.chdir pkgdir

    rm FileList["*.pkg.tar.gz"], :verbose => true
    rm_rf %w(pkg src), :verbose => true
    sh "makepkg -f"

    pkg = FileList["*.pkg.tar.gz"].first

    repo = File.read("PKGBUILD") =~ /^arch=.*any/ ? 'any' : ARCH
    cp pkg, "../repo/#{repo}/#{pkg}", :verbose => true

    touch buildok

    sh "yaourt -U #{pkg}"
  end

  task :all_packages => pkgname
end

###################################

namespace :clean do
  desc 'Clean package files'
  task :packages do
    rm_f FileList['*/**.pkg.tar.gz']+FileList['*/.build_ok']
  end
end

desc 'Build all packages'
task :all_packages

task :default => :all_packages

###################################

package :mysql_ruby_enterprise => :ruby_enterprise
package :ruby_enterprise
package :passenger_apache2 => [:passenger_common, :ruby_enterprise]
package :passenger_common
