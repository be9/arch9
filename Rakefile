ARCH = `arch`.strip
REPO = 'arch9'

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

    cp pkg, "../repo/#{ARCH}/#{pkg}", :verbose => true

    touch '.build_ok'

    sh "yaourt -U #{pkg}"

    Dir.chdir '..'
  end

  task :all_packages => pkgname
end

###################################

namespace :clean do
  desc 'Clean package files'
  task :packages do
    rm_f FileList['*/**.pkg.tar.gz']+FileList['*/.build_ok']
  end

  desc 'Clean repos'
  task :repos do
    rm_f FileList['repo/*/**.pkg.tar.gz']+FileList['repo/*/**.db.tar.gz']
  end
end

###################################

desc 'Update repo indexes'
task :update_indexes => "repo:#{ARCH}"

namespace :repo do
  [:i686, :x86_64].each do |arch|
    task arch do
      path = "repo/#{arch}"
      db = File.join(path, "#{REPO}.db.tar.gz")
      pkgs = FileList[File.join(path, "*.pkg.tar.gz")]

      rm_f db
      sh "repo-add", db, *pkgs
    end
  end
end

desc 'Build all packages'
task :all_packages

task :default => [:all_packages, :update_indexes]

###################################

package :mysql_ruby_enterprise => :ruby_enterprise
package :fastthread_ruby_enterprise => :ruby_enterprise
package :ruby_enterprise
package :passenger_enterprise_apache2 => [:passenger_enterprise_common, :fastthread_ruby_enterprise]
package :passenger_enterprise_common => :ruby_enterprise
package :passenger_enterprise_nginx => :passenger_enterprise_common
package :gitosis_git
package :spawn_fcgi
