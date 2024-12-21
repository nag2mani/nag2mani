import React from 'react'
import { Card, CardContent, CardDescription, CardFooter, CardHeader, CardTitle } from "@/components/ui/card"
import { Badge } from "@/components/ui/badge"
import { Button } from "@/components/ui/button"
import { Separator } from "@/components/ui/separator"
import { Github, Linkedin, Mail, Code2, BookOpen, Trophy } from 'lucide-react'

export default function GitHubProfile() {
  return (
    <div className="min-h-screen bg-gray-900 text-gray-100 py-12 px-4 sm:px-6 lg:px-8">
      <Card className="max-w-4xl mx-auto bg-gray-800 shadow-xl">
        <CardHeader className="text-center">
          <CardTitle className="text-4xl font-bold text-purple-400">Nagmani Kumar</CardTitle>
          <CardDescription className="text-xl text-gray-400">A passionate software developer from India</CardDescription>
        </CardHeader>
        <CardContent className="space-y-8">
          <div className="flex flex-col md:flex-row items-center justify-between">
            <img 
              src="https://cdn.dribbble.com/users/1162077/screenshots/3848914/programmer.gif" 
              alt="Coding" 
              className="w-full md:w-1/2 rounded-lg shadow-lg mb-4 md:mb-0"
            />
            <div className="space-y-4 text-center md:text-left md:w-1/2 md:pl-8">
              <p>
                <Badge variant="secondary" className="text-sm">
                  <img src="https://komarev.com/ghpvc/?username=nag2mani&label=Profile%20views&color=0e75b6&style=flat" alt="Profile views" />
                </Badge>
              </p>
              <p className="flex items-center justify-center md:justify-start">
                <Code2 className="mr-2" /> I'm currently building <a href="https://github.com/nag2mani/airozgar" className="text-purple-400 hover:underline">AI Rozgar</a>
              </p>
              <p className="flex items-center justify-center md:justify-start">
                <Mail className="mr-2" /> How to reach me: <a href="mailto:nag2mani@gmail.com" className="text-purple-400 hover:underline">nag2mani@gmail.com</a>
              </p>
              <p className="flex items-center justify-center md:justify-start">
                <Linkedin className="mr-2" /> Connect with me on <a href="https://linkedin.com/in/nag2mani" className="text-purple-400 hover:underline">LinkedIn</a>
              </p>
              <p className="flex items-center justify-center md:justify-start">
                <Trophy className="mr-2" /> Fun fact: In coding, a bug was named after a literal insect found in early computers.
              </p>
            </div>
          </div>

          <Separator className="bg-gray-700" />

          <div>
            <h3 className="text-2xl font-semibold mb-4 text-center text-purple-400">Connect with me</h3>
            <div className="flex justify-center space-x-4">
              <Button variant="outline" size="icon" asChild>
                <a href="https://linkedin.com/in/nag2mani" target="_blank" rel="noopener noreferrer">
                  <Linkedin className="h-5 w-5" />
                  <span className="sr-only">LinkedIn</span>
                </a>
              </Button>
              <Button variant="outline" size="icon" asChild>
                <a href="https://kaggle.com/nagmanikumar" target="_blank" rel="noopener noreferrer">
                  <BookOpen className="h-5 w-5" />
                  <span className="sr-only">Kaggle</span>
                </a>
              </Button>
              <Button variant="outline" size="icon" asChild>
                <a href="https://www.leetcode.com/nag2mani" target="_blank" rel="noopener noreferrer">
                  <Code2 className="h-5 w-5" />
                  <span className="sr-only">LeetCode</span>
                </a>
              </Button>
            </div>
          </div>

          <Separator className="bg-gray-700" />

          <div>
            <h3 className="text-2xl font-semibold mb-4 text-center text-purple-400">Languages and Tools</h3>
            <div className="flex flex-wrap justify-center gap-4">
              {['bootstrap', 'css3', 'django', 'figma', 'git', 'html5', 'java', 'javascript', 'linux', 'mongodb', 'mysql', 'nodejs', 'pandas', 'python', 'pytorch', 'scikit_learn', 'tensorflow'].map((tool) => (
                <img key={tool} src={`https://raw.githubusercontent.com/devicons/devicon/master/icons/${tool}/${tool}-original.svg`} alt={tool} className="w-10 h-10" />
              ))}
            </div>
          </div>
        </CardContent>
        <CardFooter className="flex flex-col items-center space-y-4">
          <img src="https://github-readme-stats.vercel.app/api?username=nag2mani&theme=dark&show_icons=true&hide_border=true" alt="GitHub stats" className="w-full max-w-md" />
          <img src="https://github-readme-streak-stats.herokuapp.com/?user=nag2mani&theme=dark&hide_border=true" alt="GitHub streak" className="w-full max-w-md" />
          <img src="https://github-readme-stats.vercel.app/api/top-langs?username=nag2mani&theme=dark&hide_border=true&layout=compact" alt="Top languages" className="w-full max-w-md" />
        </CardFooter>
      </Card>
    </div>
  )
}

