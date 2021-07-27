require 'rake/clean'

cname = 'font_8x16.c'

task :default => [:otb, :pcf, :psfu, :c]
task :otb     => 'cnxt.otb'
task :pcf     => 'cnxt.pcf'
task :psfu    => 'cnxt.psfu'
task :c       => cname

CLEAN.add '*.psf', '*.256.*', '*.512.*', '*.sorted', '*.inc'
CLOBBER.add '*.otb', '*.psfu', '*.pcf', '*.neat.bdf', cname

def chars(filename)
	a = open(filename).read.split(/\s*/).sort
	a[96..127] + [' '] + a[0..95] + a[128..511];
end

rule '.sorted' => '.glyphs' do |t|
	c = chars t.source
	open(t.name, 'w') do |f|
		c.each_index do |i|
			ahead = i != 0
			ii = i + 1
			f.print c[i],
				if ahead && ii % 128 == 0 && ii != c.length
					"\n\n"
				elsif ahead && ii % 32 == 0 
					"\n"
				else
					' '
				end
		end
	end
end

rule cname => ['cnxt.inc', 'cnxt.bdf'] do |r|
	# Counting number of bytes in .inc file by counting comas
	nbytes = 0
	File.foreach(r.source){ |l| nbytes += l.count(',') } 
	# Get width and height
	width = 0
	height = 0
	File.foreach(r.prerequisites[1]) do |l| 
		if l.start_with? 'FONTBOUNDINGBOX'
			words = l.split(' ')
			width = words[1]
			height = words[2]
		end
	end
	defname = 'FONTDATAMAX'
	File.open(r.name, 'w') do |f|
		mode = 0
		File.foreach('/usr/src/linux/lib/fonts/' + r.name) do |l|
			case mode
			when 0
				l.gsub!(/(#define +#{defname} +)\d+/, '\1' + nbytes.to_s)
				mode = 1 if l.include? '0 }, {'
				f.write(l)
			when 1
				File.foreach(r.source){ |l| f.write(l) }
				mode = 2
			when 2
				if l[0] == '}'
					mode = 3
					f.write(l)
				end
			when 3
				value = 0
				if l.include? '.width'
					value = width
				elsif l.include? '.height'
					value = height
				end
				if value != 0
					l.gsub!(/(.*=).*,/, '\1 ' + value.to_s + ',')
				end
				f.write(l)
			end
		end
	end
end

rule '.neat.bdf' => '.bdf' do |r|
	word = 'STARTCHAR'
	File.open(r.name, 'w') do |fw|
		File.open(r.source) do |fr|
			until fr.eof? do
				l = fr.gets
				if l.start_with?(word)
					l2 = fr.gets
					fw.write(word + ' U+' + l2.split(' ')[1].to_i.to_s(16).upcase.rjust(4, '0') + "\n")
					fw.write(l2)
				else
					fw.write(l)
				end
			end
		end
	end
end

rule '.512.uni' => '.glyphs' do |r|
	open(r.name, 'w'){|f| chars(r.source).join.unpack('U*').each{|c| f.puts('%04X' % c) }}
end

rule '.512.bdf' => ['.bdf', '.512.uni'] do |r|
	sh "./ucstoany.pl +f -o #{r.name} #{r.source} k 1 #{r.prerequisites[1]} "
end

rule '.psfu' => ['.512.bdf', '.dup'] do |r|
	sh "./bdftopsf.pl -- #{r.source} #{r.prerequisites[1]} > #{r.name}"
end

rule '.psf' => '.256.bdf' do |r|
	sh "./bdftopsf.pl -- #{r.source} > #{r.name}"
end

rule '.pcf' => '.bdf' do |r|
	sh "bdftopcf -o #{r.name} #{r.source}"
end

rule '.256.bdf' => '.bdf' do |r|
	sh "./ucstoany.pl -o #{r.name} #{r.source} IBM 437 ibm-437.uni"
end

rule '.inc' => '.psf' do |r|
	sh "psf2inc #{r.source} > #{r.name}"
	sh "sed -i '0,/Char height/d' #{r.name}"
end

rule '.otb' => '.bdf' do |r|
	sh "fonttosfnt -o #{r.name} #{r.source}"
end
