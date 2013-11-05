# == Dependencies ==============================================================

require 'rake'
require 'yaml'
require 'fileutils'
require 'rbconfig'

# == Configuration =============================================================

# Set "rake watch" as default task
task :default => :watch

# Load the configuration file
CONFIG = YAML.load_file("_configs.yml")

# Get and parse the date
DATE = Time.now.strftime("%Y-%m-%d")

# Directories
POSTS = "_posts"
DRAFTS = "_drafts"

# == Helpers ===================================================================

# Execute a system command
def execute(command)
  system "#{command}"
end

# Chech the title
def check_title(title)
  if title.nil? or title.empty?
    raise "Please add a title to your file."
  end
end

# Transform the filename and date to a slug
def transform_to_slug(title, extension)
  characters = /("|'|!|\?|:|\s\z)/
  whitespace = /\s/
  "#{title.gsub(characters,"").gsub(whitespace,"-").downcase}.#{extension}"
end

# Read the template file
def read_file(template)
  File.read(template)
end

# Save the file with the title in the YAML front matter
def write_file(content, title, directory, filename)
  parsed_content = "#{content.sub("title:", "title: \"#{title}\"")}"
  File.write("#{directory}/#{filename}", parsed_content)
  puts "#{filename} was created in '#{directory}'."
end

# Create the file with the slug and open the default editor
def create_file(directory, filename, content, title, editor)
  if File.exists?("#{directory}/#{filename}")
    raise "The file already exists."
  else
    write_file(content, title, directory, filename)
    if editor && !editor.nil?
      sleep 1
      execute("#{editor} #{directory}/#{filename}")
    end
  end
end

# Get the "open" command
def open_command
  if RbConfig::CONFIG["host_os"] =~ /mswin|mingw/
    "start"
  elsif RbConfig::CONFIG["host_os"] =~ /darwin/
    "open"
  else
    "xdg-open"
  end
end

# == Tasks =====================================================================

# rake post["Title"]
desc "Create a post in _posts"
task :post, :title do |t, args|
  title = args[:title]
  template = CONFIG["post"]["template"]
  extension = CONFIG["post"]["extension"]
  editor = CONFIG["editor"]
  check_title(title)
  filename = "#{DATE}-#{transform_to_slug(title, extension)}"
  content = read_file(template)
  create_file(POSTS, filename, content, title, editor)
end


def move_with_path(src, dst)
  FileUtils.mkdir_p(File.dirname(dst))
  FileUtils.mv(src, dst)
end


# rake draft["Title"]
desc "Create a post in _drafts"
task :draft, :title, :category do |t, args|
  title = args[:title]
  category = args[:category]
  template = CONFIG["post"]["template"]
  extension = CONFIG["post"]["extension"]
  editor = CONFIG["editor"]
  check_title(title)
  filename = transform_to_slug(title, extension)
  filename = "#{category}/#{DATE}-#{filename}"

  content = read_file(template)

  #make sure category folder exists for this 

  puts "Checking if #{DRAFTS}/#{category} exists"
  FileUtils.mkdir_p("#{DRAFTS}/#{category}") unless File.directory? "#{DRAFTS}/#{category}"

  create_file(DRAFTS, filename, content, title, editor)

#make the image dir corresponding to this post
  Dir.mkdir("images/" + category) unless File.exists?("images/" + category)   
  imgdir = "images/#{category}/#{DATE}-#{title.downcase.gsub( /[^a-zA-Z0-9_\.]/, '-')}"
  Dir.mkdir(imgdir) unless File.exists?(imgdir) # make the dir unless it exists

end

# rake publish
desc "Move a post from _drafts to _posts"
task :publish do
  extension = CONFIG["post"]["extension"]
  #files = Dir["#{DRAFTS}/*.#{extension}"]
  Dir.glob("#{DRAFTS}/**/*.#{extension}").each_with_index do |file, index|
  #files.each_with_index do |file, index|
    puts "#{index + 1}: #{file}".sub("#{DRAFTS}/", "")
  end
  print "> "
  number = $stdin.gets
  if number =~ /\D/
    filename = files[number.to_i - 1].sub("#{DRAFTS}/", "")
    FileUtils.mv("#{DRAFTS}/#{filename}", "#{POSTS}/#{DATE}-#{filename}")
    puts "#{filename} was moved to '#{POSTS}'."
  else
    puts "Please choose a draft by the assigned number."
  end
end


def move_with_path(src, dst)
  FileUtils.mkdir_p(File.dirname(dst))
  FileUtils.mv(src, dst)
end


# rake publisher
desc "Move a post from _drafts to _posts"
task :publishers do
  extension = CONFIG["post"]["extension"]
  #files = Dir["#{DRAFTS}/*.#{extension}"]
  files = Dir.glob("#{DRAFTS}/**/*.#{extension}")
  #files = Dir.glob("#{DRAFTS}/**/*.#{extension}").each_with_index do |file, index|
  files.each_with_index do |file, index|
    puts "#{index + 1}: #{file}".sub("#{DRAFTS}/", "")
  end
  print "> "
  number = $stdin.gets
  if number =~ /\D/
    filename = File.basename(files[number.to_i - 1])
    #filename = File.basename(files[number.to_i - 1].sub("#{DRAFTS}/", "")
    filepath = files[number.to_i - 1]
    category = File.dirname(filepath).sub("#{DRAFTS}/", "")
    
    #puts  "post/data-filename: #{POSTS}/#{DATE}-#{filename}"
    #puts "filepath "  + filepath
    #puts "filename "  + filename
    #puts "category: #{category}"
    #puts  "post/category/date-filename: #{POSTS}/#{category}/#{DATE}-#{filename}"

    move_with_path("#{filepath}", "#{POSTS}/#{category}/#{filename}")
    #FileUtils.mv("#{DRAFTS}/#{filename}", "#{POSTS}/#{DATE}-#{filename}")
    puts "#{filename} was moved to '#{POSTS}'."
  else
    puts "Please choose a draft by the assigned number."
  end
end

# rake img 
desc "Move a post from _drafts to _posts"
task :img do
  extension = CONFIG["post"]["extension"]
  #files = Dir["#{DRAFTS}/*.#{extension}"]
  files = Dir.glob("#{DRAFTS}/**/*.#{extension}")
  files.each_with_index do |file, index|
    puts "#{index + 1}: #{file}".sub("#{DRAFTS}/", "")
  end
  print "> "
  number = $stdin.gets
  if number =~ /\D/
    filename = files[number.to_i - 1]
    basefilename =  File.basename(filename, '.*')    
    open(filename, 'a') do |f|

    onlyfilename = File.basename(files[number.to_i - 1])
    #filename = File.basename(files[number.to_i - 1].sub("#{DRAFTS}/", "")
    filepath = files[number.to_i - 1]
    category = File.dirname(filepath).sub("#{DRAFTS}/", "")

    puts "basefilename " + basefilename
    puts "filename " + filename
    puts "onlyfilename " + onlyfilename
    puts "filepath: " + filepath
    puts "category: " + category

    puts "Inserting image tags into #{filepath}"    
      Dir["images/#{category}/#{basefilename}/*"].each do |i|
        f << "![Title](#{i} \"Alt Text\")\n" 
        puts "added: #{i}"
      end
    end

  else
    puts "Please choose a draft by the assigned number."
  end
end



# rake page["Title"]
# rake page["Title","Path/to/folder"]
desc "Create a page (optional filepath)"
task :page, :title, :path do |t, args|
  title = args[:title]
  template = CONFIG["page"]["template"]
  extension = CONFIG["page"]["extension"]
  editor = CONFIG["editor"]
  directory = args[:path]
  if directory.nil? or directory.empty?
    directory = "./"
  else
    FileUtils.mkdir_p("#{directory}")
  end
  check_title(title)
  filename = transform_to_slug(title, extension)
  content = read_file(template)
  create_file(directory, filename, content, title, editor)
end

# rake build
desc "Build the site"
task :build do
  execute("jekyll build")
end

# rake watch
# rake watch[number]
# rake watch["drafts"]
desc "Serve and watch the site (with post limit or drafts)"
task :watch, :option do |t, args|
  option = args[:option]
  if option.nil? or option.empty?
    execute("jekyll serve --watch")
  else
    if option == "drafts"
      execute("jekyll serve --watch --drafts")
    else
      execute("jekyll serve --watch --limit_posts #{option}")
    end
  end
end

# rake preview
desc "Launch a preview of the site in the browser"
task :preview do
  port = CONFIG["port"]
  if port.nil? or port.empty?
    port = 4000
  end
  Thread.new do
    puts "Launching browser for preview..."
    sleep 1
    execute("#{open_command} http://localhost:#{port}/")
  end
  Rake::Task[:watch].invoke
end

# rake deploy["Commit message"]
desc "Deploy the site to a remote git repo"
task :deploy, :message do |t, args|
  message = args[:message]
  branch = CONFIG["git"]["branch"]
  if message.nil? or message.empty?
    raise "Please add a commit message."
  end
  if branch.nil? or branch.empty?
    raise "Please add a branch."
  else
    Rake::Task[:build].invoke
    execute("git add .")
    execute("git commit -m \"#{message}\"")
    execute("git push origin #{branch}")
  end
end

# rake transfer
desc "Transfer the site (remote server or a local git repo)"
task :transfer do
  command = CONFIG["transfer"]["command"]
  source = CONFIG["transfer"]["source"]
  destination = CONFIG["transfer"]["destination"]
  settings = CONFIG["transfer"]["settings"]
  if command.nil? or command.empty?
    raise "Please choose a file transfer command."
  elsif command == "robocopy"
    Rake::Task[:build].invoke
    execute("robocopy #{source} #{destination} #{settings}")
    puts "Your site was transfered."
  elsif command == "rsync"
    Rake::Task[:build].invoke
    execute("rsync #{settings} #{source} #{destination}")
    puts "Your site was transfered."
  else
    raise "#{command} isn't a valid file transfer command."
  end
end
